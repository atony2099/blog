
---
title: Lock
date: 2021-11-17
description:
tags: ["locker"]
draft: false
---


[  race condition](https://www.techtarget.com/searchstorage/definition/race-condition#:~:text=A%20race%20condition%20is%20an,sequence%20to%20be%20done%20correctly.)

[Data Races vs Race Conditions - Cronokirby - https://cronokirby.com/posts/2019/06/data-races-vs-race-conditions/data-races-vs-race-conditions/](https://cronokirby.com/posts/2019/06/data-races-vs-race-conditions/data-races-vs-race-conditions/)

##  data race and race condition

### race conditon 

在并发的程序里
1. 产生不确定行为,non-determinism
3. 我们预期程序总是产生特定行为

```go
func main() {
    go func() {
        for {
            fmt.Println("Thread B")
        }
    }
    for {
        fmt.Println("Thread A")
    }
}
```
如果我们期望按照  threadA-threadb 交替输出，则是race conditon 



### data race 
1. 多个线程同时访问一个变量
2. 至少有一个是写入操作










```




未获得预期的结果
1. 多个线程并发执行, 访问了共享资源
2. 正确的结果需要依赖多个线程按照特定顺序执行. 
3. 但是多线程并未按照特定的顺序执行, 线程执行的顺序不可预期的

1. In concurrent programming,
2. a race condition occurs when the correct behavior of a program depends on the order or timing of two or more threads' execution
3. and that order or timing is unpredictable.



type：
critial race conditon:   发生了race  condition, 但是结果不影响程序的正确性
non-critical  conditon: 发生了race conditon, 但影响程序正确性

non-critail race conditon 
```


```


###  data race



## problem:







what:
1. 一种同步机制; 
2.  确保资源只能被一个线程


code: 
```
acquire(loccker)
	if get: go
	if not get get: block 


release 


```


how:
```
int  flag = 0;

locker:
try set new value:
	success: get locker  
	fail: wait




unlock:rest flag
flag=0
```



locker type:

1. optimistic locker:   
	2. check flag and do,  check fail: rollback or return 
	3. no block other thread 
```c 
select version, name from  user // tang,2 

if success =   [update name=jim  where  name =tang and  version=2],!success{
	rollback
}



compareandswap(flag,0, 1):
	if flag==0: flag =1;
	else  return;


```
1. pessimistic  locker:   
	2.  a: set flag and do,  set fail:  wait
	3. block other thread 
	


## distributed  lock


how: 
1. 获取标识
2. 重置标识 
```c
compareandswap  flag

unset flag 
```


```c

uuid
while(1):
	result = SET  locker  uuiid  NX EX 300
	if result == nil: continue
	else: break;


//.....
if  get locker == uuid: del
	else return;
	 
```


key pointer:
1. 设置过期时间(被动释放 )，防止锁无法释放


probem:  
1. 线程执行时间过长被动释放 ，另一线程获取锁
	1. 老线程释放新线程锁： add random value 
	2. 在临界区有两个线程: watch dog 续期， redissession
![HzQaJn](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/HzQaJn.jpg)
2. 集群问题:  非强一致性
	1. 主备切换时候
	2. 脑裂




### etcd  

how：
1. try set min verison:
	1.  set:   revision  = /locker/uuid and keepalive
	2.  checkSuccess:
		1.  success: the min revison of  /locker/
		2.  fail: wait pre  key
3. unlock:   delete  key 

```
// compare and swap 

leaseid = etcd lease grant  10
etcdctl lease keep-alive  leaseid
reponse  = put /locker+randid '' -- lease leaseid


// the min revision 
list  = etcdctl get  --prefix=/locker --sort-by  modify 


if  response.modversion = list[0].modversion:
	get locker..,..

else
	watch previous key 
	

```






## dead lock 

what:  线程阻塞，等待锁的释放， 而由于循环等待， 锁永远不会被释放  

example:
两个进程，互相持有对方需要的锁

the  conditon:
1. resouce(locker):
	1. 互斥:be obtain only one owner
	2. 不可剥夺:  relase by owner
3. cycle wait : 循环等待， 持有上一个进程需要的锁， 等待下一个资源释放锁 
		P0   wait p1.locker  , Pn wait P0.locker(n>=1)
		![ZKUPys](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/ZKUPys.png)





```
p1：
func getAge:
	locker1.lock;
	//dosth
	locker2.lock  
	///dosth..
	locker2.unlock
	locker1.unlock

p2： 
func getName():
	locker2.lock；
	//dosth
	locker1.lock ;
	//dosth
	locker1.unlock;
	locker2.unlock;
	


```



how:
1. before:   avoid
2. doing:  react to 
3. after
















