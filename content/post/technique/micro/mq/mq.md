---
title: message queue
date: "2021-09-26T08:03:10+0800"
tags: ["mq"]
---


## overview

what: 
1. 发送消息;
2. 进程间通信

components:
producer  ---> broker ----->  consumer;

pros: 
进程间通信方式 ， 应用解耦
1.   reliabilty:  continue work when  different  part of your system  go offine 
2.  high performce
	1. async:  producer  continue work  without waiting consumer   
	2.  Scalability:  按需增加 producer,consumer, queue

cons: 
1. 无法即时得到反馈

use  case: 
1. 验证码，邮件
2. 大流量:    日志，秒杀 








## 3. message queue 对比；

![YAbVuJ](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210926/YAbVuJ.jpg)




##  kafka  overview; 
![GzJUbP](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210926/GzJUbP.jpg)


### 1.  structure 

![PX6rUT](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210928/PX6rUT.jpg)


topic = N个partition(N replicas)

![FjJyb0](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20201215/FjJyb0.jpg)


### 1. 可靠性保证；


发送保证:

1. request.required.acks:


接收保证: 




1. min.insync.replicas:  
2. 分布式架构；
3. 





## 2. consumer

1. how to cnsume


2. kafka 是有序的吗? 如何有序；



## batch produce and consumer

producer
```
batch.size =  16kbytes;
```

consumer
```
max.poll.records

```




## 2. 为什么kafka 快；
1. partition并行处理；
2. 顺序写入(追加写入);
3. zero copy;


## 3. tuning (调优);
impore throughout;
1. producer
    1. batch.size =  16kbytes;
    2. linger.ms  = 0;
2. broker:
3. producer:
4. 批量读写, 并进行压缩；



## 3. 是否会重复消费； 如何解决?


不会； 什么机制保障； 


1. xx

## kafka 消息传递的过程；

1. producer: 
   1. createTopic:
       ``` 
       key:  key | null;
       timestamp: xxx;
    ```
    2.  send to broker ;
2. broker; 
    1. 写入某个partition;
        1. 明确指定 
        2. 有key则hash, 无key则轮询(round-robin)





3. consumer：


## consumer
