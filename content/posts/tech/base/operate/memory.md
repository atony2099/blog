---
title: "内存"
date: 2020-04-29T21:26:51+08:00
draft: false
categories: ["operation"]
---

[Linux的内存分页管理](https://www.cnblogs.com/vamei/p/9329278.html)

[Virtual Memory](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-004-computation-structures-spring-2017/c16/c16s1/)

[Writing a Memory Allocator](http://dmitrysoshnikov.com/compilers/writing-a-memory-allocator/)

## memory layout
![1aS8Nw](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220220/1aS8Nw.jpg)

1. initialized data(data): global/static 
2.  Uninitialized Data Segment: gloabl/static


## 虚拟内存

操作系统对物理内存做了屏蔽，进程只能访问到虚拟内存

![QuRtf3](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20200825/QuRtf3.jpg);
![84qMjy](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20200825/84qMjy.jpg)

mmu(memory management unit) 再通过page table 将虚拟内存地址转换成物理内存地址




## 分页
1. 为什么
如果不分页，那么 page table 将特别大；
2. 如何
   `getconf PAGE_SIZE`
   一般每页为4kb；也即是内存管理的最小单位


## 转换(翻译)过程
![9rTlb5](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20200825/9rTlb5.jpg)
![](https://images2018.cnblogs.com/blog/413416/201807/413416-20180718150907259-1604778032.png)

通过编号+偏移量进行住转换


### 3. io 
[磁盘IO瓶颈](https://segmentfault.com/a/1190000021388785)


#### 1. summary 

![](https://segmentfault.com/img/bVbBUgY)



#### 1. throughput
> throughput: the total size of read/write operations per second
> IOPS: the time of read/write operations per second  

Throughput = IOPS   x  block size








程序访问是虚拟内存地址，操作系统会将虚拟内存地址映射物理内存地址;


为提高物理内存查找效率，将虚拟内存和物理内存分成页，（4kb~16kb）

虚拟内存通过page map 映射成对应物理内存地址
![](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-004-computation-structures-spring-2017/c16/c16s1/Slide08.png)


## 常见的内存分配
[TCMalloc解密](https://wallenwang.com/2018/11/tcmalloc/)

### 1. TCMalloc
![](https://wallenwang.com/wp-content/uploads/tcmalloc-ThreadCache.svg)
![](https://wallenwang.com/wp-content/uploads/tcmalloc-Overview.svg)
![](https://wallenwang.com/wp-content/uploads/tcmalloc-SpanList.svg)

TCMalloc分配内存的基本单位是Span,由n个page构成

核心思想：多级分配，减少锁的使用，本地线程分配则是完全无锁操作。

1. 小对象分配，(0, 256KB]： 每个线程维护一份本地缓存（Thread cache），分配对象时候从本本地缓存找到对应的 size class上,查找class所指向的单链表上的第一个空闲对象
2. 中等对象[256KB, 1MB]: 从pageheap中查找对应的page



## 磁盘
扇区：存储最基本(小)单位，通常为512byte;
块:操作系统I/O 的基本(小)单位；




## run time

1. allocate
   1. static allocate: 
      1. create in stack;
      2. location, size are determined at compile time
   2. runtime allocate
      1. crete in heap
      2. location, size are determined at c







