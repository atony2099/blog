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

[[Golang三关-典藏版] Golang 调度器 GMP 原理与调度全分析](https://learnku.com/articles/41728)

## overview

### over

![1qtA3o](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210911/1qtA3o.jpg)
![](https://img.draveness.me/2020-02-05-15808864354595-golang-scheduler.png)

![YYkPj1](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220418/YYkPj1.png)

1. what:
   1. 调度系统: go实现的调度系统，
   2. feature:
      1. run in user space;
      2. 协作式调度

2. role:
   1. g: todo task,  a light thread;
   2. proceessor: connector
   3. machine: 运行得实体,对应一个os thread

### 1.  processor

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

3. why need processor?
 1. cache local g: speed up access g;
 2. cache mcache: allocate new memory;

4. 为什么不使用 machine缓存?
 machine will sleep when system call;

5. the number of processor?
 1. defaul:  numbercpu;
 2. set: runtime.GOMAXPROCS(n)

### 2. machine
  
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

### 3. G

#### 1. basic

1. what？ 
 light thread;

2. process vs  thread  vs  goroutine;
 1. same:
       1. 对cpu而言，他们都是一样的， to do instruction;
       2. struct{insrtuctsList, memory }

 2. share  trend;
  1. share time slice: p->t->g;
  2. share memory: p.heap ->t,g
     > 减少切换成本->提高cpu使用效率；
 
3. state
 ![GjS7Qr](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210914/GjS7Qr.jpg)
 ![wkwbum](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220720/wkwbum.jpg)

 1. runable: to be excuted
 2. running: be excuted
 3. wait:
       1. gwait->runable
       2. gsyscall:
          1. running
          2. runable

4. G is cheap?
 1. less memroy: dynamic  stack
 2. less switch time:
       1. less registe;
       2. in use space, not need interrupt

#### 2. struct

```go
type g struct {
 stack struct {
  lo uintptr
  hi uintptr
 }        // 栈内存：[stack.lo, stack.hi)
 stackguard0 uintptr
 stackguard1 uintptr

 _panic       *_panic
 _defer       *_defer
 m            *m    // 当前的 m
 sched        gobuf
 stktopsp     uintptr  // 期望 sp 位于栈顶，用于回溯检查
 param        unsafe.Pointer // wakeup 唤醒时候传递的参数
 atomicstatus uint32
 goid         int64
 preempt      bool        // 抢占信号，stackguard0 = stackpreempt 的副本
 timer        *timer         // 为 time.Sleep 缓存的计时器

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

#### 4. G0

1. what's
   1. machine first g;
   2. do schdule work
2. case?
 1. new g: go func()->systemstack(func(){...})
 2. park/ready

## init

![q4R0JD](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211219/q4R0JD.jpg)

1. how:
   1. 创建 first machine, 加入allm;
   2. 创建 ncpu processor,first processor 绑定machine0;
   3. 创建 main G: func= main，绑定processor0;
   4. run first machine

## schedule

![img](https://colobu.com/2017/05/04/go-scheduler/go-sched.png)
![](https://static.studygolang.com/190526/6c28ca553fa01ea792c4f73a2133c5e0.png)
![xoSeop](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210825/xoSeop.jpg)

1. how? do a polling (check status continuously)
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
 
2. stop   G?
   1. wait  another g:
 
   2. wait  system resouce

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

#### 2.preempt

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

   ```
 localg.count>256;

   ```

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
