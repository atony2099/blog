---
title: concurrency model 
date: "2021-11-12T03:06:36+0800"
draft: false
categories: ["concurrency"]
---

[Concurrency Models](http://tutorials.jenkov.com/java-concurrency/concurrency-models.html)

[Thread Safety and Immutability](http://tutorials.jenkov.com/java-concurrency/thread-safety-and-immutability.html)



## model list

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



example search: 

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

case:







#### 









## thread safe;

why: 多线程同时修改统一变量，读写非原子性导致另一个线程数据被覆盖；

a=100
a thread: a=a+1;  a=100; a=100+1 101;
b thread: a=a+2;  a=100; ........... a=100+2 102;

> a thread updating is overwrite by thread b;


## model;
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

