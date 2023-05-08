---
title:  channel and csp;
date: "2021-09-17T19:20:17+0800"
draft: false
categories: ["go","scheduler","concurrency"]
---
[souce code](https://golang.org/src/runtime/chan.go)

[Go并发编程(十) 深入理解 Channel](https://lailin.xyz/post/go-training-week3-channel.html#%E4%BD%BF%E7%94%A8%E9%80%9A%E4%BF%A1%E5%85%B1%E4%BA%AB%E5%86%85%E5%AD%98)

[Golang源码探索(二) 协程的实现原理](https://www.1024sou.com/article/205713.html)

[Goroutine Leaks - The Forgotten Sender](https://www.ardanlabs.com/blog/2018/11/goroutine-leaks-the-forgotten-sender.html)

[The Behavior Of Channels Author image](https://www.ardanlabs.com/blog/2017/10/the-behavior-of-channels.html)

## what?
1. what's a channel?
	message queue between g; 
	 g: g<-chan-->g


## mechanisam
### 1.structure

![ufo484](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210916/ufo484.jpg)

```go
type hchan struct {
    qcount   uint           // total data in the queue
    dataqsiz uint           // size of the circular queue
    buf      unsafe.Pointer // points to an array of dataqsiz elements
    elemsize uint16
    closed   uint32
    elemtype *_type // element type
    sendx    uint   // send index
    recvx    uint   // receive index
    recvq    waitq  // list of recv waiters
    sendq    waitq  // list of send waiters

    lock mutex
}

type sudog struct{
   g *g
   isSelect bool
   next *sudog
   prev *sudog
   elem unsafe.Pointer //data element
   ...
}
```



1. channel structure
	channle{Gqueuue, Databuffer};


###  how?

1. overview
   1. block: gopark(g);
   2. unblock: goready(waitq.g)



2. send:
   1. block: have no wait g  or  buffer is full;
      1. gopark g, sendq.add g: 当前g进入等待状态，excute next g
   2. unblock: have wait g or buffer is not full;
      1. copy data to waitg or buffer;
      2. if receiveq.length != 0, goready(receive.g): 等待的g被重新唤醒，即将被调度；
 

3. receive:
   1. block: have no wait g  or buffer is empty;
      1. gopark g, receiveq.add g
   2. unblock: have wait g or buffer is not empty
		1. copy data from wait g or buufer;
		2. if sendq.length !=0, goready(sendq.g)





###  goReady

1. pseudo code	
	1. g.statue=runable;
	2. runput;


2. code:

   ```go
   func goready(gp *g, traceskip int) {
   	systemstack(func() {
   		ready(gp, traceskip, true)
   	})
   }

   // Mark gp ready to run.
   func ready(gp *g, traceskip int, next bool) {
   	if trace.enabled {
   		traceGoUnpark(gp, traceskip)
   	}

   	status := readgstatus(gp)

   	// Mark runnable.
   	_g_ := getg()
   	mp := acquirem() // disable preemption because it can be holding p in a local var
   	if status&^_Gscan != _Gwaiting {
   		dumpgstatus(gp)
   		throw("bad g->status in ready")
   	}

   	// status is Gwaiting or Gscanwaiting, make Grunnable and put on runq
   	casgstatus(gp, _Gwaiting, _Grunnable)
   	runqput(_g_.m.p.ptr(), gp, next)
   	wakep()
   	releasem(mp)
   }
   ```

### 4.gopark 

1. pseudo code
	```
	g.status=waiting;
	scheuele();
	```


2. code

```go
func gopark(unlockf func(*g, unsafe.Pointer) bool, lock unsafe.Pointer, reason waitReason, traceEv byte, traceskip int) {
	if reason != waitReasonSleep {
		checkTimeouts() // timeouts may expire while two goroutines keep the scheduler busy
	}
	mp := acquirem()
	gp := mp.curg
	status := readgstatus(gp)
	if status != _Grunning && status != _Gscanrunning {
		throw("gopark: bad g status")
	}
	mp.waitlock = lock
	mp.waitunlockf = *(*unsafe.Pointer)(unsafe.Pointer(&unlockf))
	gp.waitreason = reason
	mp.waittraceev = traceEv
	mp.waittraceskip = traceskip
	releasem(mp)
	// can't do anything that might move the G between Ms here.
	mcall(park_m)
}
func park_m(gp *g) {
	// g0
	_g_ := getg()

	if trace.enabled {
		traceGoPark(_g_.m.waittraceev, _g_.m.waittraceskip)
	}
	//线程安全更新gp的状态，置为_Gwaiting
	casgstatus(gp, _Grunning, _Gwaiting)
	// 移除gp与m的绑定关系
	dropg()

	if _g_.m.waitunlockf != nil {
		fn := *(*func(*g, unsafe.Pointer) bool)(unsafe.Pointer(&_g_.m.waitunlockf))
		ok := fn(gp, _g_.m.waitlock)
		_g_.m.waitunlockf = nil
		_g_.m.waitlock = nil
		if !ok {
			if trace.enabled {
				traceGoUnpark(gp, 2)
			}
			casgstatus(gp, _Gwaiting, _Grunnable)
			execute(gp, true) // Schedule it back, never returns.
		}
	}
	// 重新做一次调度
	schedule()
}
```





##  channel  state 

state:
1. nil 
1. open
2. close

send data to:
1. closed channe: panic
2. nil channel: block


read data  from
1. closed channel: zero value
2. nil channel: block  

close:
1. closed channel: panic 
2. nil channel: panic 


why send and close chanel panic;
1. 从源码上看，会panic；
2. 有意的设计, 防止数据丢失
	1. consumer will exit if channel close
	2. noone will recieve the channel, the message will lost 

how close channel work:
1. channel.closed = true;
2. wakeup all g in waiting queue;


check channel is closed:
```go
data, open :=<-c
if !open print("closed")



data range channel{
	...
}
fmt.Println("now is cloese")

```


## concurrency  types

 1. parallel workers(share data):   do task at  one  thread 
 
![W1S4xz](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220208/W1S4xz.jpg)

```c
g1  handle data; Locker update data UnLocker  
g2  ...
```

assembly line(communicate):  do  task in two parts

![uk9dir](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220208/uk9dir.jpg)

producer: handle data, work g
consumer: update data: cacaulte g
``` c
g1 handle data    ->   g3: update data
g2 handle data    ->   g3: update data
```





##  assembly  line 

 pros:
1.  no need locker 
2.  more 调理


models 

1.  1 G -> 1 G
	  ```
	  go: channel<- data
	  go: <-channel

	  ```
2. N G->1 G: fan-in,  most common  
```c
	 for data range datas: go run data

	 for data range channel: updateby data
```

3.  1 G -> N G -> 1: pool  



example

```c
	producer:
   		go:  queryMysql(data1) ;
   		go:  queryMysql(data2)

	consumer and new producer:
	 	
		for data range c{
			go: queryES(data)
		}
		// or 
		for data range c{
			list.append(data)
		}
		go ES(data[0]+ data[1])
		go ES(data[1]+ data[2])

	consumer:
		for range channel

```


code
   ```go
	func search(c chan, ctx context):
		var  innerChannel = make(chan int, 1)
		
		go:	
			data = doSearch(innerChannel);
			innerChannel <- data;
		select:
			case <-ctx.done(): //
				return;
			case data := <-innerChannel:
				 	outChannel <-data

   ```


   




##  g   leak;

what: g can't exit 

case:
1. be blocked by channel
```go
go func(){
	<-c // wait forever 
}()
	
```


how:
principle,  who produce , who close

1. producer  wait and close
```c

go func1: defer wg.done
go func2:  defer wg.done;

go func: wg.wait; close(chanel)

```

2.  buffer channel : consumer exit first 
```c
go func: buffer<-data

go func: 
	select 
		<-bufer 
		 <-ctx
```





## buffer channel 

buffer:  在缓存前阻塞，非同步
unbuffer: 立即阻塞,同步


use case:










1. not block and not sync 
2. block only if 
	1. producer: buffer is full
	2. consumer: bufer is empty 

vs unbuffer channle:
1. not block,  block only channle is full(block producer) or empty(block consumer )
2. producer and consumer not sync


use case:
*use feature:  block only full or empty *

1.  avoid leak: producer, block only channel full
```c
go sender:
	buffer<- data
```
2. control  max: producer, block only channel full;

3. ordercontrol: 
	1. producer block only if channel full: g1
	2. consuemr block only if channel empty g2


```c
g1:
	c1<-1
	dosth 
	c2<-1  // unblock c2
	c1<-1 // block me 
	dosth //
	c2<-1 // unblock c2
	c1<-1 // block me 
	...


g2:
	<-c2 // block me
	dosth 
	<-c1 // unbock g1
	<-c2 //block me 
	dosth 
	<-c1 // unblock c1
	<-c2 // block me 
```




##  wait-closed  and cancel


### wait-closed


core:
1. *send* notificatin after g end, by work g
2. *listen* notification at one g, by  count g


channel:  
```c 

go func1():
	defer signalChan<-
	do sth...

go func2(): 
	....


for{
	<-signalChann<-
	finish++;
	if finish == num:
		close(c)
}
```


wait.group : 
```
w.Add(N);

go fun1:
	defer w.Done
	....

go: w.wait()
```




###  cancel


producer send  cancel signal, consumer listen signal

```c

func longJob: ctx,cha // producer
	select:
		case <-ctx.Done():
			return
		case data <-innerChan:
			cha<-data
			
	go:
		data = query()
		innerChan<-dat
	
```








## pool


simple: not resue the g 
```go
controlChannel = make(chan struct{},poolSize);
controlChannel <-  struct{}{};
go func(){

}()

```



complex:
1    generator --(fan-out)-->N * go consumer----(fan-in)-->1;

```go
// generator
for data := range datas:
	dataC<-data



// consumer1
go for:
	defer  wg.done;
	v,ok := <-dataC
	if !ok return ;
	excute task
	resultC <- result 

// consumer2
go for:
	....

// --- -----
go countG:
	wg.wait
	close(resultC)


for result  range  resultG:
		
```







