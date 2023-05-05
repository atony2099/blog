---
title: lock
date: "2021-09-11T21:51:51+0800"
draft: false
tags: ["go","locker","map"]
---

[Does one assembler instruction always execute atomically? [duplicate]](https://stackoverflow.com/questions/1091099/does-one-assembler-instruction-always-execute-atomically)

[多图详解Go的互斥锁Mutex](https://www.cnblogs.com/luozhiyun/p/14157542.html)

[Go并发编程(四) 深入理解 Mutex](https://lailin.xyz/post/go-training-week3-sync.html#%E8%A7%A3%E9%94%81-1)

[【golang】sync.Mutex互斥锁的实现原理](https://segmentfault.com/a/1190000023874384)

[go 读写锁实现原理解读](https://segmentfault.com/a/1190000039712353)

[Semaphores in Process Synchronization](https://www.geeksforgeeks.org/semaphores-in-process-synchronization/)

[Lecture 6: architectural support for synchronization](https://www.cs.cornell.edu/courses/cs4410/2015su/lectures/lec06-spin.html)

[详解go中的混合锁 - mutex](https://codeantenna.com/a/VgEC68sPlH)

[源码解读 Golang 的 sync.Map 实现原理](https://juejin.cn/post/6844904100287496206)

[深度解密Go语言之sync.map](https://zhuanlan.zhihu.com/p/344834329)

[ Deep Understanding of Golang Mutex](https://levelup.gitconnected.com/deep-understanding-of-golang-mutex-9964b02c56e9)

## read-write atomic instruction




1. what？
   读和写原子操作，是实现锁的基础；

2. test and set

  ```
  testAndSet(lockPtr *int) int{
    int oldValue = *lockPtr;
    *lockPtr =  1
    return oldValue
  }
  ```


3. compare and swap 
 
  ```c
  compareAndSwap(p  *lockPtr,old int,new int) bool{
    if *p=old{
      return false;
    }
    *p = new;
    return true
  }
  ```


4. 实现锁
    1. testandset
       ```c
         volatile int lock = 0;
         void lock() {
          while (test_and_set(&lock) == 1);

          critical section  // only one process can be in this section at a time
         
          lock = 0;  // release lock when finished with the critical section
         }
       ```
    2. compare and swap
        ```c
         volatile int lock = 0;
         void lock(threadID) {
          while (compare_and_swap(&lock,0,threadID));
          
          critical section  // only one process can be in this section at a time

          lock = 0;  // release lock when finished with the critical section
         }
       ```




## Locker 

why:  data-race and cause overwrite 

date-race condition:
1. mutiple thread access same  data
2. at least one thread write the data

  ```c
   thread one:  i++;       thread two: i++；
       指令周期	处理器一	    处理器二
   0	reg = load(&counter);	 
   1	reg = reg + 1;	        reg = load(&counter);
   2	store(&counter, reg);	  reg = reg + 1;
   3	                        store(&counter, reg);
  ```




2. locker core:
    set flag and block;
    set  flag(test and set or compare and swap)
    if  ok: enter critical section;
        else:  block: spinwait or  sleep;


3. data-race(数据竞争): 
   一组操作的非原子性，中间被其他操作乘虚而入，导致数据丢失
    在多线程环境下，一组操作: data = readData(), update(data);;  
  1. mutiple thread access the same memory location;
  2. at one of the access is for writing;


1. locker type
  1. 是否允许并发读:
     1. mutex: no
     2. read/write locker:yes
  2. 是否阻塞:
    1. 悲观锁: 阻塞
    2. 乐观锁: 不阻塞，若与预期不一致则放弃



2. Locker type:

    1. mutex:
        1. mutex;
        2. read/write mutex locker
    2. spin locker 
    3. semaphore
   


3. semaphore?

    1. wait;
        p(s): 
          1. if s == 0; block, push curren to queue;;
          2. s -1; 
     
    2. signal(release)
        v(s)
          1. s+1;
          2. if s>0; wakeup first  g in queue;






## GO Locker

types:
1. mutex
2.  RWMutex 

	
###  mutex


![8QJXe9](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210912/8QJXe9.jpg)



how:
```c
Locker:
	true = compareAndswap(0,1)

	if true: do sth;
		else: 
			busy wait:  do spin;
			sleep: add wait queue
		
Unlocker:
	atomic.AddInt32(&m.state, -mutexLocked)

	wait g:
		1. starve:  starve g get locker dierctly
		2. un starve:  wakeup g and new g grab the locker
	

	
```



code:
![eYc41K](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/eYc41K.jpg)

```go
const (
    mutexLocked = 1 << iota // mutex is locked
    mutexWoken  //2
    mutexStarving //4
    mutexWaiterShift = iota //3
)
type Mutex struct {
	state int32
	sema  uint32
}
```


   ```go

   func (m *Mutex) Lock() {
       // 1.未上锁，正常返回
       if atomic.CompareAndSwapInt32(&m.state, 0, mutexLocked) {
           return
       }
       // Slow path (outlined so that the fast path can be inlined)
       m.lockSlow()
   }


   func (m *Mutex) lockSlow() {
   	var waitStartTime int64 // 等待时间
   	starving := false // 是否处于饥饿状态
   	awoke := false // 是否处于唤醒状态
   	iter := 0 // 自旋迭代次数
   	old := m.state
   	for {
   		// Don't spin in starvation mode, ownership is handed off to waiters
   		// so we won't be able to acquire the mutex anyway.
   		if old&(mutexLocked|mutexStarving) == mutexLocked && runtime_canSpin(iter) {
   			// Active spinning makes sense.
   			// Try to set mutexWoken flag to inform Unlock
   			// to not wake other blocked goroutines.
   			if !awoke && old&mutexWoken == 0 && old>>mutexWaiterShift != 0 &&
   				atomic.CompareAndSwapInt32(&m.state, old, old|mutexWoken) {
   				awoke = true
   			}
   			runtime_doSpin()
   			iter++
   			old = m.state
   			continue
   		}
     
     new := old
     if old&mutexStarving == 0 {
     	// 如果当前不是饥饿模式，那么将mutexLocked状态位设置1，表示加锁
     	new |= mutexLocked
     }
     if old&(mutexLocked|mutexStarving) != 0 {
     // 如果当前被锁定或者处于饥饿模式，则waiter加一，表示等待一个等待计数
     new += 1 << mutexWaiterShift
     }
     // 如果是饥饿状态，并且已经上锁了，那么mutexStarving状态位设置为1，设置为饥饿状态
     if starving && old&mutexLocked != 0 {
     new |= mutexStarving
     }
     // awoke为true则表明当前线程在上面自旋的时候，修改mutexWoken状态成功
     if awoke { 
       if new&mutexWoken == 0 {
         throw("sync: inconsistent mutex state")
       }
       // 清除唤醒标志位
       new &^= mutexWoken
     }
     
     if atomic.CompareAndSwapInt32(&m.state, old, new) {
         // 1.如果原来状态没有上锁，也没有饥饿，那么直接返回，表示获取到锁
         if old&(mutexLocked|mutexStarving) == 0 {
           break // locked the mutex with CAS
         }
         // 2.到这里是没有获取到锁，判断一下等待时长是否不为0
         // 如果不为0，那么加入到队列头部
         queueLifo := waitStartTime != 0
         // 3.如果等待时间为0，那么初始化等待时间
         if waitStartTime == 0 {
           waitStartTime = runtime_nanotime()
         }
         // 4.阻塞等待
         runtime_SemacquireMutex(&m.sema, queueLifo, 1)
         // 5.唤醒之后检查锁是否应该处于饥饿状态
         starving = starving || runtime_nanotime()-waitStartTime > starvationThresholdNs
         old = m.state
         // 6.判断是否已经处于饥饿状态
         if old&mutexStarving != 0 { 
           if old&(mutexLocked|mutexWoken) != 0 || old>>mutexWaiterShift == 0 {
             throw("sync: inconsistent mutex state")
           }
           // 7.加锁并且将waiter数减1
           delta := int32(mutexLocked - 1<<mutexWaiterShift)
           if !starving || old>>mutexWaiterShift == 1 { 
             // 8.如果当前goroutine不是饥饿状态，就从饥饿模式切换会正常模式
             delta -= mutexStarving
           }
           // 9.设置状态
           atomic.AddInt32(&m.state, delta)
           break
         }
         awoke = true
         iter = 0
       } else {
         old = m.state
   	}
    
     	
   }

   ```

###  RWMutex
what:
read unlock, write lock

how:
```C
Read:
	readcount++; 
	if readcount> 0; read;

write:
	readcount-=maxcount;
	add mutex

```


``
   ```go
	type RWMutex struct {
	
		w Mutex // held if there are pending writers
	
		writerSem uint32 // semaphore for writers to wait for completing readers
	
		readerSem uint32 // semaphore for readers to wait for completing writers
	
		readerCount atomic.Int32 // number of pending readers
	
		readerWait atomic.Int32 // number of departing readers
	
	}

   func (rw *RWMutex) RLock() {
       if atomic.AddInt32(&rw.readerCount, 1) < 0 {
           // 如果readerCount小于0则通过同步原语阻塞住，否则将readerCount加1后即返回
           runtime_SemacquireMutex(&rw.readerSem, false, 0)
       }
   }

   // 总结一下Lock的流程：1. 阻塞新来的写操作；2. 阻塞新来的读操作；3. 等待之前的读操作完成；
   func (rw *RWMutex) Lock() 
       // 通过rw.w.Lock阻塞其它写操作
       rw.w.Lock()
       // 将readerCount减去一个最大数（2的30次方，RWMutex能支持的最大同时读操作数），这样readerCount将变成一个小于0的很小的数，
       // 后续再调RLock方法时将会因为readerCount<0而阻塞住，这样也就阻塞住了新来的读请求
       r := atomic.AddInt32(&rw.readerCount, -rwmutexMaxReaders) + rwmutexMaxReaders
       // 等待之前的读操作完成
       if r != 0 && atomic.AddInt32(&rw.readerWait, r) != 0 {
           runtime_SemacquireMutex(&rw.writerSem, false, 0)
       }
   }

   ``` 



## sync.map and slice

why map,slice  isn't safe:
no protection  in data-race

when: concurrency write  data
```c
go func1:  
	map[data]=value

go func1:  
	append: append s 
	update: s[0]= xx;
	 
```


example:
```c
go setMap[key1] = 100;
go setMap[key2] = 200;
key1, key2, bucket 0;

read available linked node 0;
							 read available linked node 0;
							
write node 0 key1 value1
						  write node 0 key1 value1
```

```c
go append(slice1,100);
go append(slice2,200)

read available index 0;
						read available index 0;
write index 0 100;
						write index 0 100
```

  

2. map error 
    runtime.thow("concurrency map write")

3. how sync.map work?
   ![C2ddcM](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220216/C2ddcM.jpg)
   ```go
    type Map struct {
    // 加锁作用，保护 dirty 字段
    mu Mutex
    // 只读的数据，实际数据类型为 readOnly
    read atomic.Value
    // 最新写入的数据
    dirty map[interface{}]*entry
    // 计数器，每次需要读 dirty 则 +1
    misses int
    }
   ```

   two map: 空间换时间
    read:
    1. read  map: 无锁操作； 只读 ，更新
    2. dirty map:  加锁操作, 新增;



##  sync.waitGroup;

1. what?
   阻塞计数器, block if waitCount>0;
  1. 等待多个协程

2. struct
   ```go
   type  struct {
   	noCopy noCopy

   	// 64-bit value: high 32 bits are counter, low 32 bits are waiter count.
   	// 64-bit atomic operations require 64-bit alignment, but 32-bit
   	// compilers do not ensure it. So we allocate 12 bytes and then use
   	// the aligned 8 bytes in them as state, and the other 4 as storage
   	// for the sema.
   	state1 [3]uint32
   }
   ```

   wg.Add(3): increase count;
   go wg.Add(-1);
   wg.wait(): block  if count > 0;

3. how to work?
    ```
    wg.Add(10);
    go fun:...;wg.Done();
    go fun:...;wg.Done();
    wg.Wait()
    ``` 

4.  error wait:
    允许传导错误 
    wg.Go(func()err{
      //...
    })
    if err := wg.Wait(); err!=nil{
      return err;
    }

