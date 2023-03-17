---
title: memory
date: "2021-08-08T03:35:26+0800"
draft: false
categories: ["linux"]
---

[【原创】（十四）Linux内存管理之page fault处理](https://www.cnblogs.com/LoyenWang/p/12116570.html)
[lec16_OS_virtual_memory](http://www.cs.uni.edu/~fienup/cs142f03/lectures/lec16_OS_virtual_memory.htm)

[What Is Swappiness on Linux? (and How to Change It)](https://www.howtogeek.com/449691/what-is-swapiness-on-linux-and-how-to-change-it/)


## 0. memory section
![LpG8Sn](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210828/LpG8Sn.png)

![tklgK9](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210901/tklgK9.jpg)


1. 分区；
   1. text(code): 代码区
   2. data：
      1. rodata: 只读区;主要存储字符串常量; const 变量;
      2. initialized data:  全局变量和static变量；
      3. uninitialized data:  未初始化；
   3. stack
   4. heap


> text and data 大小在加载程序后就确定了;


2. 非连续空间:
   只有堆是非连续空间;一开始是连续的， 在分配过程破碎。

3. 回收；
   1. 不需回收: code,data; 存在整个程序声明周期；
   2. 自动回收:  stack;基于栈这种数据结构， 在方法return后这段栈上分配的空间就都释放了；
   3. 需要垃圾回收器: 堆；

   

4. 静态存储区: 代码区，数据区
   > 存在整个生命周期,不需回收; 除了bbs, 其他数据在程序启动的时候就创建好；
   动态存储区: 堆，栈
   > 存在一段时间, 需要回收；
   





## ram

### overview 

![O2cYo1](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210806/O2cYo1.jpg)

### page table entries (PTE);
ram被分成 n份 `PAGE_SIZE`；

程序不能直接读写ram，而是读写中间层virtual memory;
pageTable是virtual memory 到 physical memory的映射；


virtual: [pageNumber][offset]
---> 
physical: [frame number][offset]



1. structure;


![1U4fF5](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210831/1U4fF5.jpg)



2. mmu和pageTable关系；
    mmu是个硬件，加载pagetable工作；
3. pageTable是每个process独有的吗?
    是；



### page fault;
找不到对应 frame,无法再执行下去，抛出一个 fault;

![8946Dl](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210807/8946Dl.jpg)



1. 是什么?
    访问某虚拟内存，但是该虚拟内存对应物理内存不存在；

2. 触发场景?
   ![BwlnxI](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210831/BwlnxI.jpg)

    1. alloc 分配内存

        ![nPVniQ](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210831/nPVniQ.jpg)

    2. 访问文件；





##  swap
1. virtual memory = physical memory(ram) + swap disk 

   ![auKZCA](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220521/auKZCA.png)

2. which
   1.  inactive app  memory;



3. swapiness:
   This control is used to define how aggressive (sic) the kernel will swap memory pages.