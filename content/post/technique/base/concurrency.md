---
title: concurrency model 
date: "2021-11-12T03:06:36+0800"
draft: false
categories: ["concurrency"]
---

[Concurrency Models](http://tutorials.jenkov.com/java-concurrency/concurrency-models.html)

[Thread Safety and Immutability](http://tutorials.jenkov.com/java-concurrency/thread-safety-and-immutability.html)

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

