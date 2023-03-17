---
title: bigdata
date: "2021-01-11T10:17:20+0800"
draft: false
categories: ["bigdata"]
---

[Redis 中 HyperLogLog 的使用场景](https://www.cnblogs.com/54chensongxia/p/13803465.html)
[布隆过滤器（附go语言代码实现，详细注释）](https://blog.csdn.net/weixin_42310154/article/details/119386707)

## bigdata 

1. what's?
当大量数据产生的时候，传统手段处理起来乏力，需要一些新的手段


## bitmap;

1. data  Structure
   bit array;一个
   ```go

   var bitmap []bit ;
   func (offset){
       bit[offset/8] |= offset%8
       
   };
   ```

2. how save space:
   >   int32: 32bit;   in bitmap 1bit  32倍
    4 billion data:  
    bitmap:  2^32 * (1/8)/1024/1024 = 512M;
    normal:  2^32*4/1024/1024 =  512M * 30 = 15G;

    
3. usecase:
   ```c
   setbit ssp_clickcount 111 1;
   bitCount ssp_clickcount 0 -1;
   ```


## HyperLogLog；
1. 实时数据分析；估算 
    ```c
    PFADD  ssp_001  
    PFCount  
    ```


2. 原理：   
    001
    00001
    0001
    0000001

    伯努利值: 一条随机的二进制数据，第一次出现1位伯努利值；
    伯努利值与数据数正指数相关
    > store: hash(value).伯努利值
    
3. 优势:
   1. 12kb





## bloom filter
使用多个hashFunction;