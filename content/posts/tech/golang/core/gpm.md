---
title: GPM
date: "2020-09-14T10:14:20+0800"
draft: false
category: ["go","scheduler"]
---

[详尽干货！从源码角度看 Golang 的调度](https://studygolang.com/articles/20651)

[调度器](https://draveness.me/golang-goroutine/)

[详尽干货！从源码角度看 Golang 的调度（上）](https://www.infoq.cn/article/r6wzs7bvq2er9kuelbqb)

[Getting Started With Golang Channels! Here’s Everything You Need to Know](https://www.velotio.com/engineering-blog/understanding-golang-channels)

[深入golang runtime的调度](https://zboya.github.io/post/go_scheduler/)

[code](https://github.com/golang/go/blob/64c22b70bf00e15615bb17c29f808b55bc339682/src/runtime/proc.go#L2446)

[Go: What Does a Goroutine Switch Actually Involve?](https://medium.com/a-journey-with-go/go-what-does-a-goroutine-switch-actually-involve-394c202dddb7)

[Go: How Does the Goroutine Stack Size Evolve?](https://medium.com/a-journey-with-go/go-how-does-the-goroutine-stack-size-evolve-447fc02085e5)

[MPG 模型与并发调度单元](https://golang.design/under-the-hood/zh-cn/part2runtime/ch06sched/mpg)

[Illustrated Tales of Go Runtime Scheduler.](https://medium.com/@ankur_anand/illustrated-tales-of-go-runtime-scheduler-74809ef6d19b)

[Go调度器系列（3）图解调度原理](https://segmentfault.com/a/1190000018775901)

[Go netpoller 原生网络模型之源码全面揭秘](https://strikefreedom.top/go-netpoll-io-multiplexing-reactor)

[图解Go运行时调度器](https://tonybai.com/2020/03/21/illustrated-tales-of-go-runtime-scheduler/)

[Go 运行程序中的线程数](https://colobu.com/2020/12/20/threads-in-go-runtime/)

[Go中的调度器(4)--goroutine状态转移](https://againest1.github.io/post/go/go%E4%B8%AD%E7%9A%84%E8%B0%83%E5%BA%A6%E5%99%A84/#gdead)

[Go 程序启动过程是怎样的](https://qcrao91.gitbook.io/go/bian-yi-he-lian-jie/go-cheng-xu-qi-dong-guo-cheng-shi-zen-yang-de)

[GMP](https://www.yixuebiancheng.com/article/91899.html)

[【深入理解Go】协程设计与调度原理（上）](https://segmentfault.com/a/1190000040710568)

[Go 为什么这么“快”](https://mp.weixin.qq.com/s/2Q_o8NLidMXc-DtoinPwSQ)

[Golang三关-典藏版 Golang 调度器 GMP 原理与调度全分析](https://learnku.com/articles/41728)

[How Goroutines Work - https://blog.nindalf.com/posts/how-goroutines-work/](https://blog.nindalf.com/posts/how-goroutines-work/)

[go runtime - go程序启动过程 ](https://juejin.cn/post/6942509882281033764)

[Go: g0, Special Goroutine](https://medium.com/a-journey-with-go/go-g0-special-goroutine-8c778c6704d8)

[sysmon 后台监控线程做了什么 - 码农桃花源](https://qcrao91.gitbook.io/go/goroutine-tiao-du-qi/sysmon-hou-tai-jian-kong-xian-cheng-zuo-le-shi-mo)

[Go中定时器实现原理及源码解析](https://www.luozhiyun.com/archives/458)



![1qtA3o](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210911/1qtA3o.jpg)

![YYkPj1](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220418/YYkPj1.png)


## what  

what: 
1. go实现的调度
2. run in user space
3. 更加高效



role:
1. g:  用户线程,轻量线程，包含等待被执行的function code 
2. processor：连接 machine 和 g
3. machine: 执行的实体, 关联一个系统线程，通过系统线程执行 
	1. 找到可以运行的g
	2. 执行g


more effective:
1. 协作式调度:更少的上下文切换
2. 轻量级线程，less create cost  and context switch cost 


m:n modal: 多个用户线程复用一个系统线程 
性能优势:
1.  较少线程创建的开销，只需要创建固定数量的线程，然后低成本创建大量routine
2.  减少 context switch:   很多block 和context switch 发生在用户空间 



**the count:**

g:  创建成本主要是stack size 2kb , plus some small  overhead for control block(g struct)  ,   1GB= 5millon g
processor: max =  the number of cpu
```go
runtime.GOMAXPROCS(runtime.NumCPU())
```
machine count: 
max running machine = processor count;  



why  go scheduler is  good   for  concurrecy :
1.  降低线程使用的开始:  线程很轻量，可以无限制的创建
2.  原生支持csp, 对程序员而言更容易实现复杂的并发
	1. 消除并发常见的问题: data-race, dead-lock 
	2.  以模块化的方式实现并发
		1. 更容易实现复杂并发: 将复杂问题分解成小模块
		2. 更容易维护:   1. 发现和调式问题 2.修改

模块化方式:
每个g 是一个模块，是一个独立的个体，与其他模块通过channel 连接






###  processor

1. state:
   ![DtWAoL](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220418/DtWAoL.jpg)
 stop->idle<->running<->systemcall

2. code

   ```go
   type p struct {
    id           int32
    status       uint32 // p 的状态 pidle/prunning/...
    link         puintptr
    m            muintptr   // 反向链接到关联的 m （nil 则表示 idle）
    mcache       *mcache
    pcache       pageCache
    deferpool    [5][]*_defer // 不同大小的可用的 defer 结构池
    deferpoolbuf [5][32]*_defer
    runqhead     uint32 // 可运行的 Goroutine 队列，可无锁访问
    runqtail     uint32
    runq         [256]guintptr
    runnext      guintptr
    timersLock   mutex
    timers       []*timer
    preempt      bool
    
    // Available G's (status == Gdead)
    gFree struct {
     gList
     n int32
    }
   }
   ```

why need processor？ processor 的详细作用

machine and goroutine的中介 
1. 缓存goroutine: machine 无锁访问

为什么不使用machine 缓存gorotine：
machine 可能 系统调用停止运行 



### machine
  
1. what? 
	1. 执行实体: run a loop
 2. 绑定一条 os thread, 通过 os thread 执行

2. structure

   ```go
   // src/runtime/runtime2.go
   type m struct {
    g0          *g   // 用于执行调度指令的 Goroutine
    gsignal     *g   // 处理 signal 的 g
    tls         [6]uintptr // 线程本地存储
    curg        *g   // 当前运行的用户 Goroutine
    p           puintptr // 执行 go 代码时持有的 p (如果没有执行则为 nil)
    spinning    bool  // m 当前没有运行 work 且正处于寻找 work 的活跃状态
    cgoCallers  *cgoCallers // cgo 调用崩溃的 cgo 回溯
    alllink     *m   // 在 allm 上
    mcache      *mcache

    ...
   }
   ```

3. new machine
   1. alloc machine;
   2. alloc os thread;

   ```go
    // Create a new m. It will start off with a call to fn, or else the scheduler.
   // fn needs to be static and not a heap allocated closure.
   // May run with m.p==nil, so write barriers are not allowed.
   //go:nowritebarrierrec
   // 创建一个新的m，它将从fn或者调度程序开始
   // fn需要是静态的，而不是堆分配的闭包。
   func newm(fn func(), _p_ *p) {
    // 根据fn和p和绑定一个m对象
    mp := allocm(_p_, fn)
    // 设置当前m的下一个p为_p_
    mp.nextp.set(_p_)
    mp.sigmask = initSigmask
    if gp := getg(); gp != nil && gp.m != nil && (gp.m.lockedExt != 0 || gp.m.incgo) && GOOS != "plan9" {
     // 我们处于锁定的M或可能由C启动的线程。此线程的内核状态可能很奇怪（用户可能已将其锁定为此目的）。
     // 我们不想将其克隆到另一个线程中。相反，请求一个已知良好的线程为我们创建线程。
     lock(&newmHandoff.lock)
     if newmHandoff.haveTemplateThread == 0 {
      throw("on a locked thread with no template thread")
     }
     mp.schedlink = newmHandoff.newm
     newmHandoff.newm.set(mp)
     if newmHandoff.waiting {
      newmHandoff.waiting = false
      notewakeup(&newmHandoff.wake)
     }
     unlock(&newmHandoff.lock)
     return
    }
    // 真正的分配os thread
    newm1(mp)
   }

   func newm1(mp *m) {
    // 对cgo的处理
    ...
    
    execLock.rlock() // Prevent process clone.
    // 创建一个系统线程，并且传入该 mp 绑定的 g0 的栈顶指针
    // 让系统线程执行 mstart 函数，后面的逻辑都在 mstart 函数中
    newosproc(mp, unsafe.Pointer(mp.g0.stack.hi))
    execLock.runlock()
   }
   ```

4. state

   ```
   mstart
    |
    v        
   +--------+       找不到可执行任务           +-------+
   |unspinin| ----------------------------> |spining| 
   |        | <---------------------------- |       |
   +--------+       找到可执行任务            +-------+
    ^                                      | stopm
    |               wakep                  v
   notewakeup <-------------------------  notesleep
   ```

    1. work:
    1. unsping:
    2. spinning: steal

    3. waiting:
       1. sleeping: not find g;
       2. systemcall

5. is machine count limit?
 1. limit the active machine count
 2. if the i/o increase, the machine count increase

### goroutine

**structure:**
gobuf:  register info,  used for context switch
stack: 当前 g使用的stack; 
stauts:  g status
m: 对应的线程 
```go

type g struct { 
	// 当前 Goroutine 的栈内存范围 [stack.lo, stack.hi)
	stack       stack 
	// 用于调度器抢占式调度  
	stackguard0 uintptr   
	goid        int64

	_panic       *_panic  
	_defer       *_defer  
	// 当前 Goroutine 占用的线程
	m            *m       
	// 存储 Goroutine 的调度相关的数据
	sched        gobuf 
	// Goroutine 的状态
	atomicstatus uint32 
	// 抢占信号
	preempt       bool // preemption signal, duplicates stackguard0 = stackpreempt
	// 抢占时将状态修改成 `_Gpreempted`
	preemptStop   bool // transition to _Gpreempted on preemption; otherwise, just deschedule
	// 在同步安全点收缩栈
	preemptShrink bool // shrink stack at synchronous safe point
	...
}

type gobuf struct {
 // 栈指针
 sp   uintptr
 // 程序计数器
 pc   uintptr
 // gobuf对应的Goroutine
 g    guintptr 
 // 系统调用的返回值
 ret  sys.Uintreg
 ...
}
```


**status**:

1. prepare: idle-> runable
2. run: running 
3. block:
	1. waiting 
	2. sysemblocking 
![RqdUVncjVkO2](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230518/RqdUVncjVkO2.jpg)

![Noo9UNlu9LwI](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230518/Noo9UNlu9LwI.jpg)


**process vs  thread  vs  goroutine:**

same:
1. 独立的指令执行路径, independent execution paths
2. 独立的stack and  register 

differ:   more lightweight
1. thread: 共享process's memory
2. goroutine:  共享(多路复用 ) thread 


**cheaper:**
1.  更低的创建成本: 
	1. 占用内存少: 2kb <=1 mb 
	2. 更快: 不需要系统调用
2.  更低的切换成本:  基于 线程复用 和协作式调度，goroutine 切换只需切换少量register


not expose go id:
1. 避免  thread local storage 的滥用，导致代码难以维护
2. 可以使用context 存储 goroutine 范围内的变量


**g0:** 

what:
特殊的协程，执行特殊的任务，不执行用户代码
1. 调度相关: 创建新g; 获取可用g
2. 其他任务: 
	1. stack grow; 
	2. defer function create;
	3. systemcall

feature:
fixed and larger size, 2MB, 在执行特殊任务需要更大空间




4. what's
   1. machine first g;
   2. do schdule work
5. case?
 1. new g: go func()->systemstack(func(){...})
 2. park/ready


## schedule

### 协作式调度

what:  线程主动放弃cpu，调度器才能调度下一个线程
vs preemptive:  调度器可以主动打断线程 重新获得cpu的控制权 


process:
1. machine 运行 loop 
2. fetch g then run:
	1. 从local queue, global queue,   
	2. steal  from  other queue 

load balance:
1.   上限：设置单个machine处理上限
2.   分担：使用steal分担其他machine的压力


give up control:

1. block:
	1.  i/o 
	2. sleep
	3. channel  
	4. wait locker
	5. **`runtime.Gosched`**

2. finish excute



#### more effective 
1.  



###  preempt

```
backgroudthread:
	if  g.running >= 10ms:
		
	   g.preempt = true
	   sendSignal to machine 
		


```


###  init


![GLbm0SOoFkGI](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230514/GLbm0SOoFkGI.jpg)


1. 创建m0,g0, 关联
2.  init:  
	1. 初始化工作: stack, heap,gc
	2. 创建 n processor 
4. 创建 g.main, 加入processor0
5. run m0 loop

code:

```c
TEXT runtime·rt0_go(SB),NOSPLIT,$0
        // 复制参数数量argc和参数值argv到栈上
        MOVQ    DI, AX          // argc
        MOVQ    SI, BX          // argv
        SUBQ    $(4*8+7), SP            // 2args 2auto
        ANDQ    $~15, SP
        MOVQ    AX, 16(SP)
        MOVQ    BX, 24(SP)
        
        // 初始化 g0 执行栈
        MOVQ    $runtime·g0(SB), DI	    // DI = g0
        LEAQ    (-64*1024+104)(SP), BX
        MOVQ    BX, g_stackguard0(DI)	    // g0.stackguard0 = SP + (-64*1024+104)
        MOVQ    BX, g_stackguard1(DI)       // g0.stackguard1 = SP + (-64*1024+104)
        MOVQ    BX, (g_stack+stack_lo)(DI)  // g0.stackguard1 = SP + (-64*1024+104)
        MOVQ    SP, (g_stack+stack_hi)(DI)  // g0.stackguard1 = SP + (-64*1024+104)
        ...
        // 该函数在 runtime/runtime1.go/check()，进行各种检查，包括类型的长度Sizeof、结构体字段的偏移量、
        // CAS操作、指针操作、原子操作、汇编指令、栈大小检查等
        CALL    runtime·check(SB)
        ...
        // 该函数在runtime/runtime1.go/args(c int32, v **byte) 进行命令行参数的初始化
        CALL    runtime·args(SB)      
        // 该函数在runtime/os_linux.go/osinit() 读取操作系统的CPU核数
        CALL    runtime·osinit(SB)    
        // 该函数在runtime/proc.go/schedinit() 调度器的初始化，涉及内存空间的初始化、命令行参数的初始化、
        // 垃圾收集器参数的初始化、调度器process的设置等
        CALL    runtime·schedinit(SB) 
        // create a new goroutine to start program
        MOVQ    $runtime·mainPC(SB), AX         // entry
        PUSHQ   AX
        PUSHQ   $0                      // arg size
        // 该函数在runtime/proc.go/newproc(siz int32, fn *funcval) 
        // 创建一个新的G，并将G放到runtime的队列中，这个G用于执行runtime.main函数
        CALL    runtime·newproc(SB)  
        
        POPQ    AX
        POPQ    AX

        // start this M
        // runtime/proc.go/mstart() 开始调度，从队列里面取G进行执行，并执行runtime.main函数
        // 在runtime.main的执行中，会依次执行runtime中的init函数、启动GC收集器、
        // 执行用户包的init函数、执行用户的main函数
        CALL    runtime·mstart(SB)

        MOVL    $0xf1, 0xf1  // crash
        RET

```


### loop



![img](https://colobu.com/2017/05/04/go-scheduler/go-sched.png)
![xoSeop](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210825/xoSeop.jpg)




machine run  a loop:
 ```
 while(true):
  isGet=getfrom
             localqueue;
             globalqueue;
             netpollerqueue; 
       stealfromotherqueue
  if isGet=true; 
   excute(g); continues;;
  else
   sleep()
 ```


highlight:
1. 使用窃取算法平衡负载, it attempt to steal work from other threds' run queue, this approach  help balances the workload among threads 
2. cooperative    schedule:  更低的开销；    less switch cost, less switch;
3. m:n model:  m个goroutine,  但是最多n个运行 


work steal:
![hrvAIkD6mK5l](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230510/hrvAIkD6mK5l.jpg)




### new G

1. code:

   ```go
   func newproc(siz int32, fn *funcval) {
    argp := add(unsafe.Pointer(&fn), sys.PtrSize)
    // 获取当前的 G 
    gp := getg()
    // 获取调用者的程序计数器 PC
    pc := getcallerpc() 
    systemstack(func() {
     // 获取新的 G 结构体
     newg := newproc1(fn, argp, siz, gp, pc)
     _p_ := getg().m.p.ptr()
           // 将 G 加入到 P 的运行队列
     runqput(_p_, newg, true)
     // mainStarted 为 True 表示主M已经启动
     if mainStarted {
      // 唤醒新的  P 执行 G
      wakep()
     }
    })
   }
   ```

### consume(schedule)

1. how:
 1. polling;
 2. excute(g);
 3. savestatue, repolling;

1. polling:
   1. local:
      1. runnext
      2. localQueue: if var g = p.queue.pop(); g !=nil  m.excute(g);
   2. global:
      1. global queue
      2. netpoller queue
   3. steal

2. order

   ``` 
   go func1: print(1);
   go func2: print(2);
   ```

   resutle: 2,1 or 1,2;
   单线程: new first, new is put in runnext; 2,1
   多线程: 被窃取， 导致可能 old g 先被执行; 无法预测执行顺序

### g stop and preempt

#### 1. 协作式（主动）

1. wait other G;
 1. mutex;
 2. channel
 3. select;
 4. sync.wait

2. how?
   1. block:  
      1. park g: g.status=wait, put g in wait queue
      2. re polling
   5. wakeup:
   1. ready g: pop from queue, put local queue

3. wait sytem resource
   1. readfile

4. how?
   1. stop:
      1. g stop: g.status=systemcall;
      2. machine 与 processor 分离;  machine are park by os;
 
   2. wakeup:
      1. machine get old processor, then idle processor:  g.status=running
      2. else: machine sleep, put  g in gloabel queue: g.status = runable;

####   preempt

1. what?
 运行过长的g会被踢出去

2. how?
 ```
 in system g:
  every ms:
   if g.runtime> n ms: 
    park g
    shedule(next g)

 ```

### strategy

### localqueue

1. why?
 no locker;

#### load balacne

1. overload: put in global queue;

 localg.count>256;



2. idle:
   1. steal from other g;
   2. get from gloabl queue

#### decrease block（free）

> 减少闲置时间；machine 充分运行

1. switch g context, machine continue run;
   1. channel,locker
   2. netpoller

### 1. schedinit(SB)

![mQAs4rfeE](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210910/mQAs4E.jpg)

1. m0 =new m;  machineArray.add(m0);
2. processArray = new ProcesssArray

```go
func schedinit() {
 // new m0
 mcommoninit(_g_.m)
 // new processList
 procresize()

}
```

### 3. wakeup

> start a new  machine;;

1. check: idleProcessor != 0  && hasNoMachinIsSpinning
2. getIdleProcessor; get Idle or create machine

```go
func wakep() {
   if atomic.Load(&sched.npidle) == 0 { // 当前没有空闲的P
      return
   }

   // be conservative about spinning threads
   if atomic.Load(&sched.nmspinning) != 0 || !atomic.Cas(&sched.nmspinning, 0, 1) {
      return
   }
   startm(nil, true)
}
```

```go
// Schedules some M to run the p (creates an M if necessary).
// If p==nil, tries to get an idle P, if no idle P's does nothing.
// May run with m.p==nil, so write barriers are not allowed.
// If spinning is set, the caller has incremented nmspinning and startm will
// either decrement nmspinning or set m.spinning in the newly started M.
//go:nowritebarrierrec
// startm是启动一个M，先尝试获取一个空闲P，如果获取不到则返回
// 获取到P后，在尝试获取M，如果获取不到就新建一个M
func startm(_p_ *p, spinning bool) {
 lock(&sched.lock)
 // 如果P为nil，则尝试获取一个空闲P
 if _p_ == nil {
  _p_ = pidleget()
  if _p_ == nil {
   unlock(&sched.lock)
   if spinning {
    // The caller incremented nmspinning, but there are no idle Ps,
    // so it's okay to just undo the increment and give up.
    if int32(atomic.Xadd(&sched.nmspinning, -1)) < 0 {
     throw("startm: negative nmspinning")
    }
   }
   return
  }
 }
 // 获取一个空闲的M
 mp := mget()
 unlock(&sched.lock)
 if mp == nil {
  var fn func()
  if spinning {
   // The caller incremented nmspinning, so set m.spinning in the new M.
   fn = mspinning
  }
  // 如果获取不到，则新建一个，新建完成后就立即返回
  newm(fn, _p_)
  return
 }

 // 到这里表示获取到了一个空闲M
 if mp.spinning { // 从midle中获取的mp，不应该是spinning状态，获取的都是经过stopm的，stopm之前都会推出spinning
  throw("startm: m is spinning")
 }
 if mp.nextp != 0 { // 这个位置是要留给参数_p_的，stopm中如果被唤醒，则关联nextp和m
  throw("startm: m has p")
 }
 if spinning && !runqempty(_p_) { // spinning状态的M是在本地和全局都获取不到工作的情况，不能与spinning语义矛盾
  throw("startm: p has runnable gs")
 }
 // The caller incremented nmspinning, so set m.spinning in the new M.
 mp.spinning = spinning //标记该M是否在自旋
 mp.nextp.set(_p_)      // 暂存P
 notewakeup(&mp.park)   // 唤醒M
}
```


## sysmon


## basic   thought

### 1. resuse

GPM的复用:

1. G.idle, ;
2. processr.idle;
3. machine.idle;

## important function

### systemCall

1. save Current G, switch to G0;
2. excute fuc
3. return to origin G;

```
// func systemstack(fn func())
TEXT runtime·systemstack(SB), NOSPLIT, $0-8
 MOVQ fn+0(FP), DI // DI = fn
 get_tls(CX)
 MOVQ g(CX), AX // AX = g
 MOVQ g_m(AX), BX // BX = m

 CMPQ AX, m_gsignal(BX)
 JEQ noswitch

 MOVQ m_g0(BX), DX // DX = g0
 CMPQ AX, DX
 JEQ noswitch

 CMPQ AX, m_curg(BX)
 JNE bad

 // switch stacks
 // save our state in g->sched. Pretend to
 // be systemstack_switch if the G stack is scanned.
 CALL gosave_systemstack_switch<>(SB)

 // switch to g0
 MOVQ DX, g(CX)
 MOVQ DX, R14 // set the g register
 MOVQ (g_sched+gobuf_sp)(DX), BX
 MOVQ BX, SP

 // call target function
 MOVQ DI, DX
 MOVQ 0(DI), DI
 CALL DI

 // switch back to g
 get_tls(CX)
 MOVQ g(CX), AX
 MOVQ g_m(AX), BX
 MOVQ m_curg(BX), AX
 MOVQ AX, g(CX)
 MOVQ (g_sched+gobuf_sp)(AX), SP
 MOVQ $0, (g_sched+gobuf_sp)(AX)
 RET

```

### mcall

1. save Current G, switch to G0;
2. excute fuc

```
TEXT runtime·mcall(SB), NOSPLIT, $0-8
 MOVQ fn+0(FP), DI

 get_tls(CX)
 MOVQ g(CX), AX // save state in g->sched
 MOVQ 0(SP), BX // caller's PC
 MOVQ BX, (g_sched+gobuf_pc)(AX)
 LEAQ fn+0(FP), BX // caller's SP
 MOVQ BX, (g_sched+gobuf_sp)(AX)
 MOVQ BP, (g_sched+gobuf_bp)(AX)

 // switch to m->g0 & its stack, call fn
 MOVQ g(CX), BX
 MOVQ g_m(BX), BX
 MOVQ m_g0(BX), SI
 CMPQ SI, AX // if g == m->g0 call badmcall
 JNE 3(PC)
 MOVQ $runtime·badmcall(SB), AX
 JMP AX
 MOVQ SI, g(CX) // g = m->g0
 MOVQ SI, R14 // set the g register
 MOVQ (g_sched+gobuf_sp)(SI), SP // sp = m->g0->sched.sp
 PUSHQ AX
 MOVQ DI, DX
 MOVQ 0(DI), DI
 CALL DI
 POPQ AX
 MOVQ $runtime·badmcall2(SB), AX
 JMP AX
 RET

```


```
import requests

start = input_data["start"]
end = input_data["end"]
task = input_data["task"]
duration = int(input_data["duration"])
project = input_data["project"]
parent = input_data["parent"]

task_request = {
    "start_time": start,
    "duration": duration,
    "task": task,
    "project": project,
    "end_time": end,
    "parent":parent,
}

response = requests.post("https://api.gohi789.top/api/tasks", json=task_request)

# Check the response status code
if response.status_code == 200:
    print("Task created successfully!")
else:
    print(f"Error creating task: {response.text}")
    print("Exiting program with error.")
    sys.exit(f"Error: {response.text}")

return {"result": response.text}
```






## sysmon

what:  system monitor 
监控线程，监控异常情况，并处理


feature: 不需要绑定p; 直接运行 

do what: 
1. 检查死锁
2.  长时间没有运行的: netpoller, GC, timerr
5. 抢占: p,g


how: 
simple code:
```go
for {
	sleep(20us)
	checkdeadlock
	check runable timer
	check poller
	check gc  
	retake
	
}
```


```go
func sysmon() {
	lock(&sched.lock)
	sched.nmsys++
	checkdead()
	unlock(&sched.lock)

	// For syscall_runtime_doAllThreadsSyscall, sysmon is
	// sufficiently up to participate in fixups.
	atomic.Store(&sched.sysmonStarting, 0)

	lasttrace := int64(0)
	idle := 0 // how many cycles in succession we had not wokeup somebody
	delay := uint32(0)

	for {
		if idle == 0 { // start with 20us sleep...
			delay = 20
		} else if idle > 50 { // start doubling the sleep after 1ms...
			delay *= 2
		}
		if delay > 10*1000 { // up to 10ms
			delay = 10 * 1000
		}
		usleep(delay)
		mDoFixup()
		now := nanotime()
if debug.schedtrace <= 0 && (sched.gcwaiting != 0 || atomic.Load(&sched.npidle) == uint32(gomaxprocs)) {
  lock(&sched.lock)
  if atomic.Load(&sched.gcwaiting) != 0 || atomic.Load(&sched.npidle) == uint32(gomaxprocs) {
    syscallWake := false
    next, _ := timeSleepUntil()
    if next > now {
      atomic.Store(&sched.sysmonwait, 1)
      unlock(&sched.lock)
      // Make wake-up period small enough
      // for the sampling to be correct.
      sleep := forcegcperiod / 2
      if next-now < sleep {
        sleep = next - now
      }
      shouldRelax := sleep >= osRelaxMinNS
      if shouldRelax {
        osRelax(true)
      }
      syscallWake = notetsleep(&sched.sysmonnote, sleep)
      mDoFixup()
      if shouldRelax {
        osRelax(false)
      }
      lock(&sched.lock)
      atomic.Store(&sched.sysmonwait, 0)
      noteclear(&sched.sysmonnote)
    }
    if syscallWake {
      idle = 0
      delay = 20
    }
  }
  unlock(&sched.lock)
}

....
```


### preempt

types:
1. processor 抢占: 长时间处于systemcall 的processor
2.  goroutine 抢占: 
	1. 设置标志位
	2. 抢占
		1. 协作式抢占:在call funtion 时候抢占
		2. 发送信号进行抢占


retake goroutine  simple code
```
if  g.running time > 10ms:
	set p.preempt = true;

	send signal 

in long run  goroutine:
	do  signal handler


```




```go
func retake(now int64) uint32 {
	n := 0
	// 遍历所有的 p
	for i := int32(0); i < gomaxprocs; i++ {
		_p_ := allp[i]
		if _p_ == nil {
			continue
		}
		// 用于 sysmon 线程记录被监控 p 的系统调用时间和运行时间
		pd := &_p_.sysmontick
		// p 的状态
		s := _p_.status
		if s == _Psyscall {
			// P 处于系统调用之中，需要检查是否需要抢占
			// Retake P from syscall if it's there for more than 1 sysmon tick (at least 20us).
			// _p_.syscalltick 用于记录系统调用的次数，在完成系统调用之后加 1
			t := int64(_p_.syscalltick)
			if int64(pd.syscalltick) != t {
				// pd.syscalltick != _p_.syscalltick，说明已经不是上次观察到的系统调用了，
				// 而是另外一次系统调用，所以需要重新记录 tick 和 when 值
				pd.syscalltick = uint32(t)
				pd.syscallwhen = now
				continue
			}
	
			// 只要满足下面三个条件中的任意一个，则抢占该 p，否则不抢占
			// 1. p 的运行队列里面有等待运行的 goroutine
			// 2. 没有无所事事的 p
			// 3. 从上一次监控线程观察到 p 对应的 m 处于系统调用之中到现在已经超过 10 毫秒
			if runqempty(_p_) && atomic.Load(&sched.nmspinning)+atomic.Load(&sched.npidle) > 0 && pd.syscallwhen+10*1000*1000 > now {
				continue
			}
			
			incidlelocked(-1)
			if atomic.Cas(&_p_.status, s, _Pidle) {
				// ……………………
				n++
				_p_.syscalltick++
				// 寻找一新的 m 接管 p
				handoffp(_p_)
			}
			incidlelocked(1)
		} else if s == _Prunning {
			// P 处于运行状态，检查是否运行得太久了
			// Preempt G if it's running for too long.
			// 每发生一次调度，调度器 ++ 该值
			t := int64(_p_.schedtick)
			if int64(pd.schedtick) != t {
				pd.schedtick = uint32(t)
				pd.schedwhen = now
				continue
			}
			//pd.schedtick == t 说明(pd.schedwhen ～ now)这段时间未发生过调度
			// 这段时间是同一个goroutine一直在运行，检查是否连续运行超过了 10 毫秒
			if pd.schedwhen+forcePreemptNS > now {
				continue
			}
			// 连续运行超过 10 毫秒了，发起抢占请求
			preemptone(_p_)
		}
	}
	return uint32(n)
}
```


抢占 g:
```

if  g running time > 10ms
	
```
