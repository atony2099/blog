---
title: process
date: "2021-07-17T17:26:09+0800"
draft: false 
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


### 3. kernel space vs user space;
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

## 2. context switch


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
