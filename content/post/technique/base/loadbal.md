---
title:  balance
date: "2021-10-28T23:35:49+0800"
draft: true
categories: ["thought"]
---

[Microservices: Client Side Load Balancing](https://www.linkedin.com/pulse/microservices-client-side-load-balancing-amit-kumar-sharma/)



## what 

tasks = [task1,task2, ...];
nodes = [node1,node2,...];

excutTaskInUnits(tasks,nodes);

如何分配任务到多个节点上,使得整体更高效



## types 

### server side:
1. types: 
   1. proxy model
2. how?
    1. client ---->proxy ->[node1,...] 

3. pros and cons:
   1. pros: 
        1. simple client: client have no awareness of backend;
   2. cons:
        1. proxy limit:
            1. limit scalability
            2. limit speed(increase latency)


 ###  client side

1. pros and cons:
    1. pros: high speed( no need extra  hop)
    2. cons: complex client


2. 进程内
    ![eQlsAV](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220403/eQlsAV.jpg)

    1. pros
        1. simple client
    2. cons:
        1. 与客户端强耦合，不利于后续维护


3. external load balancing 独立进程
    ![T7rVtq](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220403/T7rVtq.jpg)
    1. pros:
        1. complex client
    2. cons: 
        1. 与客户端解耦









## strategy

1. round-roubin:轮询，按顺序
2. least-connection/responseTime/....:
3. hash



### 1. service 



