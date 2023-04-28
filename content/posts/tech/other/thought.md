---
title:  thoughts of program
date: "2021-10-22T01:03:09+0800"
draft: false
categories: ["thought"]
---


> ariticl state: TOOD

在整个繁杂的科学计算机, 是由一些基本的编程思想构建起来的， 编程思想，一个简单的定义就是
> 处理问题的方法论；

容器，微服务...各种牛逼技术日新月异，但各种技术最终追求的目标是一致的: 
    1. 更快；
    2. 更强；
    3. 更易维护拓展； 
基于这些共同目标， 编程思想最终会殊途同归；

## 多路复用(multiplex)思想

1. 是什么: 同时处理好几个任务(数据流)；
2. 如何做: 
   1. 多个执行实体并行处理
        1. 多核操作系统；
        2. 负载均衡器(nginx)
   2. 单个执行体，并发处理， 任务流（数据流）切割成N个单位；
        1. 单核多线程
        2. http2；

3. strategy:
    1. 多个执行实体: 每个执行实体分配的机会
         1.  round-robin(轮询)；
         2.  

    2. 单个执行实体: 每块任务流(数据流)权重 
## 高内聚，低耦合；；
由面向对象编程首创，可以推广到微服务里；


## expand/shrink  strategy
1. incremental: 递增的
   use case:  operation will block a long time
2. absoulte: 全量
   use case: operation  won't block a long time;






## convention over configuration;

do less configuration;
> decrase the developer's decision;






##  time and space 

time:  the amount of time it task  
space: the amount of memory it require to perform the task 

