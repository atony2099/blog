---
title: process  and concurrency
date: "2021-07-17T17:26:09+0800"
draft: false 
tags: ["scheduler","concurrency"]
categories: ["linux"]
---

[理解系统负载](https://lotabout.me/2018/how-system-load-is-calculated/)

[https://cloud.tencent.com/developer/article/1710837](https://cloud.tencent.com/developer/article/1710837)

[Linux进程上下文切换过程context_switch详解--Linux进程的管理与调度(二十一）](https://cloud.tencent.com/developer/article/1368753)

[【原创】（一）Linux进程调度器-基础](https://www.cnblogs.com/LoyenWang/p/12249106.html)

[Linux进程管理（二）进程调度](https://blog.csdn.net/weixin_42462202/article/details/102887008)

[进程用户态 上下文切换需要保存哪些_Linux内核浅析-进程调度时机和过程](https://daimajiaoliu.com/daima/7b7bd412c298006)

[What is Context Switching in Operating System?](https://afteracademy.com/blog/what-is-context-switching-in-operating-system)

[x86 Handling Exceptions](https://hackernoon.com/x86-handling--lds3uxc)
[INTERRUPTS](https://learnlinuxconcepts.blogspot.com/2014/02/interrupts.html)

[interrupt](https://blog.actorsfit.com/a?ID=01000-2a8aa9c6-67de-4e7b-a080-c6b51c152961)

[进程切换：自愿(VOLUNTARY)与强制(INVOLUNTARY)](http://linuxperf.com/?p=209)

[and interrupt handling](http://www.it.uu.se/education/course/homepage/os/vt18/module-1/exception-and-interrupt-handling/)

[CPU Scheduling](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/6_CPU_Scheduling.html)

Concurrency Models](http://tutorials.jenkov.com/java-concurrency/concurrency-models.html)

[Thread Safety and Immutability](http://tutorials.jenkov.com/java-concurrency/thread-safety-and-immutability.html)



## 1. process 

###  structure
![ixeByh](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210805/ixeByh.jpg)
对要执行任务的一种抽象；包含一系列相关状态；

```c
struct task_struct {
 // Process status
    /* -1 unrunnable, 0 runnable, >0 stopped: */
 long     state;
 // Virtual memory structure
 struct mm_struct  *mm;
 // Process number
 pid_t     pid;
 // Pointer to parent process
 struct task_struct __rcu  *parent;
 // Children form the list of natural children:
 struct list_head  children;
 // Pointer to filesystem information:
 struct fs_struct  *fs;
 // Open file information:
 struct files_struct  *files;
};
```


### creat thread

```c
int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
                   void *(*start_routine) (void *), void *arg) {
    struct task_struct *new_thread;

    // Allocate memory for new thread's task_struct
    new_thread = allocate_task_struct();
    if (!new_thread) {
        return -1;
    }

    // Initialize the task_struct (simplified)
    new_thread->state = TASK_RUNNING;
    new_thread->flags = 0;
    new_thread->prio = DEFAULT_PRIORITY;
    new_thread->mm = NULL;
    new_thread->active_mm = current->active_mm;  // Share memory map with current task

    // Allocate and set up the new thread's user stack and kernel stack
    new_thread->user_stack = allocate_user_stack();
    new_thread->kernel_stack = allocate_kernel_stack();
    setup_new_thread_context(new_thread, start_routine, arg);

    // Add new_thread to the runqueue so that the scheduler can start it
    add_to_runqueue(new_thread);

    // Return the new thread's ID
    *thread = new_thread->pid;

    return 0;
}

void setup_new_thread_context(struct task_struct *new_thread, void *(*start_routine) (void *), void *arg) {
    // Set up the new thread's kernel stack and CPU context
    // so that it will start execution in start_routine when scheduled.
    setup_new_thread_kernel_stack_and_context(new_thread->kernel_stack, start_routine, arg);

    // Initialize the new thread's user stack
    setup_new_thread_user_stack(new_thread->user_stack, arg);
}

void setup_new_thread_kernel_stack_and_context(void *stack, void *(*start_routine) (void *), void *arg) {
    // Make room on the stack for the return address and the function argument
    stack -= sizeof(void *);

    // Put the argument onto the stack
    *((void **)stack) = arg;

    // Initialize the new thread's context
    struct cpu_context *context = &new_thread->context;

    // Set up the instruction pointer to point to start_routine
    context->ip = (unsigned long)start_routine;

    // Set up the stack pointer
    context->sp = (unsigned long)stack;
}

void setup_new_thread_user_stack(void *user_stack, void *arg) {
    // The actual implementation of this function can be quite complex
    // and is beyond the scope of this pseudocode. In general, this function
    // would set up the new thread's user stack so that it's ready to start
    // executing in user mode.
}

```


### id
1. pid: process  id; 每个task都会有
2. tgid: task groud id；线程组id;
3. groud leader: 主task(主线程)；

初始:
[pid 1 tgid 1 groupLeaderID 1] 
创建子线程:
[pid 2 tgid 1 groupLeaderID 1]


### 2. process vs thread

![qaQzcZ](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210908/qaQzcZ.jpg)

1. 同: 都是同一种数据结构task表示， 对于内核调度来说没区别;
userView:       process 1           thread 2            
            [process pid 1 tgid 1] [thread pid 2 tgid 1]
---------------------------------------------------------
 kernelView:     process1                   process2

2. 不同: 
   Thread共享: openfile,vm;
   process没有共享的；


###  kernel space vs user space;
![JgnZgKDNeIHO](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230512/JgnZgKDNeIHO.jpg)


不同的内存区域,每个区域包含 memory(stack,heap) + instruction

user space: 
1. 运行普通指令
2. user stack, user heap

kernel space:
1. 允许较高级的指令:  i/o, 进程管理 
2. kernel stack , kernel heap 


```c
// Thread struct definition
struct Thread {
    void* userStackPointer;
    void* userInstructionPointer;
    ....
 
};


// Pseudo code for thread switch from user space to kernel space
void switchToKernelSpace(Thread* currentThread) {
    // Save the current stack pointer
    currentThread->stackPointer = getCurrentStackPointer();
    
    // Save the current instruction pointer
    currentThread->instructionPointer = getCurrentInstructionPointer();
    
    // Switch to kernel stack
    switchStackToKernel();
    

    
    // Set the stack pointer to the kernel stack pointer
    setCurrentStackPointer(getKernelStackPointer());
    
    // Set the instruction pointer to the kernel mode entry point
    setCurrentInstructionPointer(getKernelEntryPoint());
    
    // Jump to the kernel mode entry point
    jumpToKernelMode();
}


	
```



process可以在用户态和内核两种状态下来回切换;

1. 用户空间: 
   存储 堆；栈；数据；
   指令: 用户代码； 
   device access: no
2. 内核空间;
   存储： 内核栈(私有)； 内核堆(所有人共有的);
   指令:  内核代码;  
   device access: yes;



### 4. process state
![4QP21k](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210809/4QP21k.jpg)

![FnmXxj](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211102/FnmXxj.jpg)


1. runable: 就绪，等待调度;
2. waiting(sleeping): i/o;锁(内存同步访问)
3. running: 执行中;

## context switch


![pB4lbW](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211101/pB4lbW.jpg)

![E0FHK3](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210908/E0FHK3.jpg)

### 1. what's context?

1. what?
   task state;
   a set of variable store In register;  

2. cause:
   1. interrupt;





### 2. types;


1. stack: esp,ebp register;
   1. esp: stack pointer
   2. ebp: base pointer

2. cpu register

3. heap;;
   1. 页表基址(pageTable 地址)register；
   2. 刷新tlb(页表缓存);

->context_switch
  ->switch_mm_irqs_off   //进程地址空间切换
  ->switch_to //处理器状态切换

### 2. 如何触发?

1. overview
   1. 系统调用(system call):同一process内
   2. 进程切换(schedule):不同process
   3. 中断:

#### 1. system call

1. 进入内核态
   1. 保存用户态register
   2. program counter 更新为内核态指令；

2. 退出内核态
   1. 恢复用户态register





## 3. interrupt and exception

![preview](https://pic2.zhimg.com/v2-eac465cf5eb96242a7429e0bc9af7765_r.jpg)
![Ztyu06](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210824/Ztyu06.jpg)
![](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210809/S0jaWj.jpg)

![K9uNGr](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220804/K9uNGr.jpg)
### 1. overview 
1. 是什么? 
   ![eGtWyB](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220819/eGtWyB.jpg)
   unexpected condition;

2. how?
   ```
   if unexpected condition:
      save and stop current process
      enter kernel;
      call handler
   restore and resume pre procss
   ```



3. 为什么需要中断?
   读外部数据的时候,不确定什么时候数据到达；cpu也不能一直在那边等待着；
   需要一种机制， 通知cpu数据已经到达，需要他去处理;
   1. 网卡:
   2. 磁盘
   3. 键盘/鼠标；

4. vs excpetion;
   1. interrupt: cause By hardware;
   2. exception: cause by cpu,software


5. exception:
   >cause by cpu
   1. trap
   2. fault
   3. abort
## 4.system call

一些资源是所有进程都可能会用到的，为了合理分配这些有限资源，内核不愿意让进程执直接操作这些资源，而是通过一组接口让进程去调用；
这写资源包括:
1. 网络
2. 文件
3. 硬件
4. 进程/线程；
      1. create/terminate
    	1. load/excute
    	2. get process attribute


### 2. 调用过程；

1. 进入内核态:trap;
2. context switch



## 5. schedule

![p5P467](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210718/p5P467.jpg)

### what?


![oefbwF](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210823/oefbwF.jpg)


1. what?
   1. selectNextTask;
   2. context_switch;

2.  只会在i/o等待的时候放生调度；
   任何程序的执行过程，大体归为两类;
   1. i/o;
   2. 计算；
 
   如果在执行计算任务时候把他调度出去，那代价非常大，现场不好还原,不会这么做；






###  algorithm

![C6H4tS](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210820/C6H4tS.jpg)

linux 根据不同任务类型；采用不同的调度方法；
1. 实时进程：
2. 普通进程:   CFS
3. 交互式进程: shell,文本编辑器;



###  调度时机
调用schedule；所有调度都在内核态;

![LYLWXS](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210908/LYLWXS.jpg)

#### 主动(voluntary)

1. 需要等待时间发生后才可以继续执行下去，此时需要暂时让出CPU;

2. 事件: 
   1.  i/o
   2.  定时器；

3. 过程
   1. set CurrentThred to waiting(sleep) state;
   2. schedule()

```c
   tap_do_read(...)
   {
       ...
   	DEFINE_WAIT(wait); //定义一个等待队列
       
   	while(!condition)
       {
           add_wait_queue(&wq_head, &wait); //将进程添加到等待队列中
           set_current_state(TASK_UNINTERRUPTIBLE); //设置进程的状态为睡眠态
           ...
               
           /* 主动调度 */
           schedule();
       
       	...
       }
   	...
}
``` 

#### preemption(unvoluntary)
线程仍可以继续执行下去，但被迫让出cpu，此时线程处于running状态

原因:
   1. 时间片用完；
   2. 被高优先级任务抢占

how:
   1. set_tsk_need_resched;
   2. schedule;

1. types;
   1. user preemption: 发生在用户态;
      1. When returning to user-space from a system call
      2. When returning to user-space from an interrupt handler

   2. kernel preemption: 发生在内核态；
      1. interrupt to kernel 
      2. 开启抢占；




## thread model

user thread:
1.  user-level lib 管理的(创建， 允许，销毁)， 程序可以直接创建
2. 包含: 用户代码， 用户栈
3. cpu  调度: 无法直接被调度， 通过关联kernel thread 被调度  
4. 如何表示:
	1.  in  POSIX,   thread_id，内核线程的标识符(句柄)
	2. 在 go中， 有专门的 结构表示 
```c
// linux
#include <pthread.h>
#include <stdio.h>

void* myThreadFun(void *vargp) {
    printf("Printing from Thread \n");
    return NULL;
}
   
int main() {
    pthread_t thread_id;
    
    printf("Before Thread\n");
    
    // Create a new thread that will execute 'myThreadFun'
    pthread_create(&thread_id, NULL, myThreadFun, NULL);
    
    // Wait for the thread to finish
    pthread_join(thread_id, NULL);
    
    printf("After Thread\n");
    exit(0);
}


```

kernel  thread:
1. kernel lib 管理的， 程序无法创建 , os 自行创建 
2. 可以被cpu 调度
3. 如何表示:    

```
 task_struct{
	..
 }
```



1:1 model: 
n:1 model: 

m: n model: 
what: 多个用户线程复用一个内核线程 
pros:
1. 低开销: 
	1. 创建线程开销: systemcall, kernel stack; 
	2. 线程切换的开销 
2. 高拓展性: 可以创建更多的线程来处理任务





## concurrency model 


分类标准:
1.   如何通信/同步数据 

**share state**:  
1. 共享状态
2. 通过共享状态通信 
![Hbihf2f5WUwh](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230505/Hbihf2f5WUwh.jpg)
example: thread
pros:  
1. 更容易编程/实现
cons:
1. 更多并发问题:  data race and deadlock


**seperate state：**
1. 独享状态
2. 通过通信传递数据； 
![CmisOqvvx1Hy](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230505/CmisOqvvx1Hy.jpg)
example: actor(eerlang, scale ), csp 

pros:
1. 较少并发的问题: data-race, deadlock
cons:
1. 实现起来更复杂:需要将任务抽象成多个子任务，再通过channel 串联起来


don't communitcate by share memory , share memory  by communicating:

不要通过共享方式进行通信；而是使用通道的方式进行通信；






## how to do in csp

csp: 
1. 任务拆分成多个子任务，任务之间通过channel 连接 

tips:
1. go routinue 一般 只读写内部数据(包含参数);并通过channel 对外传输数据
 2. sub task type:
	1. producer: 并发获取数据
	2. consumer : 如果涉及到数据合并，不能并发

csp model:
1. 1-chan->1:常见
2. n-chan->1:最常见 
3. 1-chan->n: pool
4. m-chan->n(m>n): 不常见

example: bank account

1. 拆分任务：
	1. 获取需要改变的类型和数据
	2. 写入balance 

```c

var  updateChan

go func: // producer 
	go:
	
		update<- 100, deposite
	
	go:
		update<- -50, withdraw


go func: // writer



	for ammout  :=  range updateChan
		balance+=amount

```



example search:  n->1,  n->1

```c



var searchChan
var dbChan 
go func:
	for _, ele := range array
		go search(ele, searchChan) // stage1,producer 

go func:
	var maps 
	for  result range   searchChan
		maps[result.url] = result.content


	go getDB(maps["google.com"],maps["facebook.com"],dbChan)
	go getDB(mapsp["twiter.com"],maps["abc.com"],dbChan)



go func:
	for result range dbChan

```





### pitfall


#### goroutine leak 

leak:  已经使用完的资源未被回收
goroutine  leak: 已使用完的goroutine.....


when: 

1. 未关闭channel 导致消费端阻塞
2. in 1->1,消费端提前退出，导致生产端阻塞


how:









## thread safe;

why: 多线程同时修改统一变量，读写非原子性导致另一个线程数据被覆盖；

a=100
a thread: a=a+1;  a=100; a=100+1 101;
b thread: a=a+2;  a=100; ........... a=100+2 102;

> a thread updating is overwrite by thread b;


## model
data=100;
data+1 =101;
data+2 = 103;

1. block; 
    a thread locked(block); then wakeup other thread;;


2. produce and consumer; 
    Ga get 1, send to Gc: data=data+1=101;
    Gb get 2, send to Gc: data=data+2=103;

    {a,b | a,b∈ producer} get change, then send to c;
    {c | c∈ consumer } update value;


3. immutabel:
    禁止修改原来变量(没有修改就没有安全问题)， 需要新创建新变量来记录修改后变化；

    thread  A:  data1 =  newData(data,1);
    thread  B:  data2 =  newData(data1,2);
    
    问题: 多线程之间如何共享修改的值

