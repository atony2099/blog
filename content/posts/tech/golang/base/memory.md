---
title:  memory
date: "2022-08-25T21:16:16+0800"
draft: false
tags: ["go","memory","garbage collector"]
---
[C&C++ CS OS“编写你自己的内存分配器”](https://soptq.me/2020/07/18/mem-allocator/)

[C数据存储(包括const存储在哪，C++不同部分我在文中用红字已指出)](https://www.cnblogs.com/yanqi0124/p/3795547.html)

[Go: Introduction to the  Analysis](https://medium.com/a-journey-with-go/go-introduction-to-the--analysis-f7610174e890)

[golang 逃逸分析详解](https://zhuanlan.zhihu.com/p/91559562)

[Golang逃逸分析原理剖析](https://juejin.cn/post/7055178269112401933)

[9.5.4 逃逸分析](https://gocompiler.shizhz.me/9.-golang-bian-yi-qi-tao-yi-fen-xi/9.5.4-tao-yi-fen-xi)

[go 内存分配](https://juejin.cn/post/7063345320779841572)

[Go: Introduction to the  Analysis](https://medium.com/a-journey-with-go/go-introduction-to-the--analysis-f7610174e890)

[Implementing malloc and free](https://medium.com/@andrestc/implementing-malloc-and-free-ba7e7704a473)

[18张图解密新时代内存分配器TCMalloc](https://cloud.tencent.com/developer/article/1785894)

[Go内存分配那些事，就这么简单](https://mp.weixin.qq.com/s/3gGbJaeuvx4klqcv34hmmw)

[TCMalloc解密](https://wallenwang.com/2018/11/tcmalloc/#ftoc-heading-22)

[tcmalloc原理剖析(基于gperftools-2.1)](http://gao-xiao-long.github.io/2017/11/25/tcmalloc/)
[C/C++内存对齐详解](https://zhuanlan.zhihu.com/p/30007037)

[底层原理：垃圾回收算法是如何设计的？](https://developer.aliyun.com/article/777750)

[从源码讲解 golang 内存分配](https://studygolang.com/articles/22652?fr=sidebar)

[Memory Management in Golang](https://www.timqi.com/2020/06/11/golang-memory-management/)

[Go: How Does the Garbage Collector Mark the Memory?](https://medium.com/a-journey-with-go/go-how-does-the-garbage-collector-mark-the-memory-72cfc12c6976 )

[ Go: Memory Management and Memory Sweep](https://medium.com/a-journey-with-go/go-memory-management-and-memory-sweep-cc71b484de05)

[[典藏版]Golang三色标记、混合写屏障GC模式图文全分析](https://segmentfault.com/a/1190000022030353)

[Concurrent Mark and Sweep(并发标记-清除)](https://www.bookstack.cn/read/gc-handbook/spilt.3.04_GC_Algorithms_Implementations_CN.md)

[7.2 垃圾收集器](https://draveness.me/golang/docs/part3-runtime/ch07-memory/golang-garbage-collector/)

[Golang 三色标记和混合写屏障](https://www.cnblogs.com/thepoy/p/14598447.html)

[GC](https://qcrao91.gitbook.io/go/gc/gc)

[图示Golang垃圾回收机制](https://zhuanlan.zhihu.com/p/297177002)

[8.2 写屏障技术](https://golang.design/under-the-hood/zh-cn/part2runtime/ch08gc/barrier/)

[图文结合，白话 Go 的垃圾回收原理](https://jishuin.proginn.com/p/763bfbd641c8)

[Golang源码探索(三) GC的实现原理](https://www.cnblogs.com/zkweb/p/7880099.html)

[golang 垃圾回收（五）混合写屏障](https://liqingqiya.github.io/golang/gc/%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6/%E5%86%99%E5%B1%8F%E9%9A%9C/2020/07/24/gc5.html)

[ Go GC 如何优化掉重新扫描协程栈](https://yangxikun.com/golang/2022/05/15/golang-gc-eliminate-rescan.html)

[详细总结： Golang GC、三色标记、混合写屏障机制](https://blog.51cto.com/u_15730090/5510574)


## escape

what:
at compile determine if variable escape to heap;


### how  

> core:  变量作用域外继续被引用;


two invariant:
1. pointer to a stack object cannot outlive that object: 
	> variable must be allocated in heap if it's pointer  outlive it 
1. pointer to stack object can't be stored in heap
	> variables must be allocated heap if it's pointer in heap




code:

```c
object graph: a-> b -> c -> root

check object grap from root
	if root outlive a && dereference(a,b)=-1;  escape
	 root = b


outlive( root,a): 
	root out of ascope:
		funtion scope:
				root is return parameter;
				a in anymouse f, root out of anymous f 	
		loop of scope
	root in heap:
		

	root is return paramenter: true
	root is out scope of a: true
		function scope;
		loop scope;
		root in hepa
	
derefenece(root,a): // a in root pointer linker 
	
```

   

  
###  case


case 1:
 ![](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/xFqZVk.jpg)
 
```go
type T struct {
    Name string
}

func escapeAnalysis(arg T) (*T, bool) {
    l1 := arg
    l1.Name = "Golang"

    l2 := &l1
    l3 := *l2
    return &l3, arg.Name == "Java"
}
// l3 escape to
```


case 2:

   ![EcvZrL](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220923/EcvZrL.jpg)

   ```go
   package main

   type T struct {
       Name string
   }

   func GetT() **T {
       var t T
       l1 := &t
       l2 := &l1
       r3 := l1
       r4 :=&r3

       var l4 **T
       l4 = l2
       l4 = r4

       return l4
   }
   // l1 escape
	
   ```

case 3: 

   ```go
   func main() {
    s := make([]int, 19999999999999)
    s[0] = 1
   }
   ```

   > make([]int, 19999999999999) s to heap

## growing stack

1. process
   1. init 2kb stack;

   2. grow::

      ```
      whenCallNewFunc:
         if morestack == not enough:
               new =newStackSize(2);
               copystack(old,new)
      ```

2. code

```go
func newstack() {
   [...]
   // Allocate a bigger segment and move the stack.
   oldsize := gp.stack.hi - gp.stack.lo
   newsize := oldsize * 2
   if newsize > maxstacksize {
       print("runtime: goroutine stack exceeds ", maxstacksize, "-byte limit\n")
      throw("stack overflow")
   }

   // The goroutine must be executing in order to call newstack,
   // so it must be Grunning (or Gscanrunning).
   casgstatus(gp, _Grunning, _Gcopystack)

   // The concurrent GC will not scan the stack while we are doing the copy since
   // the gp is in a Gcopystack status.
   copystack(gp, newsize, true)
   if stackDebug >= 1 {
      print("stack grow done\n")
   }
   casgstatus(gp, _Gcopystack, _Grunning)
}

func shrinkstack(gp *g) {
 gstatus := readgstatus(gp)
 if gstatus&^_Gscan == _Gdead {
  if gp.stack.lo != 0 {
   // Free whole stack - it will get reallocated
   // if G is used again.
   stackfree(gp.stack, gp.stackAlloc)
   gp.stack.lo = 0
   gp.stack.hi = 0
   gp.stkbar = nil
   gp.stkbarPos = 0
  }
  return
 }
 // 收缩目标是一半大小
 oldsize := gp.stackAlloc
 newsize := oldsize / 2
 // Don't shrink the allocation below the minimum-sized stack
 // allocation.
 if newsize < _FixedStack {
  return
 }
 // 如果使用空间超过1/4, 则不收缩
 avail := gp.stack.hi - gp.stack.lo
 if used := gp.stack.hi - gp.sched.sp + _StackLimit; used >= avail/4 {
  return
 }
 // 用较小的栈替换当前的栈
 copystack(gp, newsize, false)
}
```

## memory alloc

1. what?
   1. alloc memory in heap;
   2. alloc memmory at runtime

2. 内存对齐: 让变量起始地址放在特性位置(填充空闲块)好让cpu一次性读完
    根本原因: cpu并不能从任意地址；它总是按照读取一定长度(word size)从特定地址访问；

![XCzXOq](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210901/XCzXOq.jpg)
上图int类型需要读取两次

1. 内存碎片: 虽然有闲置的内存无法再使用；
    主要分配机制导致;非固定大小，使用完无法再分配；
2. 为什么tcmalloc没有内存碎片问题；
    内存块分类；

### simple heap  manage

```c
struct block{  

   free bool
   size int

   next *block

}

struct list {
   header *block
   tail  *block
}; 

func alloc(needsize int) *block{
   next = list.headeer;

   // 1. get free block 
   while(next != null) {
       if !next.free || next.size< needsize) {
          next=next.next;  
       } else {
         next.free = false;
         return next+1;
      }
   }

   // 2. create new block
    *block =blk(needsiz);

   list.tail.next=block;

   return block+1


   
   

}

func free(b *block){
   b.free = false;
}



```

### overview

![iKdbr6](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210831/iKdbr6.jpg)

1. allocator: 如何分配；
2. collector: 如何回收；

#### 1.最原始的内存分配

freelist: 存储空闲块

alloc:  
    1. 从freelist寻找合适块
    2. 按需取block；将剩余的block重新加入freelist

free: 释放这个块，并把这个block重新加入freelist;

核心问题: 对freelist的读写;

### 2. TCMalloc

多级缓存:
![1KT6jW](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210831/1KT6jW.jpg)

![nAOpsu](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210901/nAOpsu.jpg)

```c
class FreeList {
private:
 void*    list_;
 uint32_t length_;
 uint32_t lowater_;
 uint32_t max_length_;
};

class ThreadCache {
private:
     FreeList      list_[kNumClasses];    
};
```

```c
  static CentralFreeListPadded central_cache_[kNumClasses];
  class CentralFreeList {
  private:
      SpinLock lock_;
      size_t size_class_;
      Span empty_;       
      Span nonempty_;
  };
```

1. 初始化；
   > freelist: 单向链表;
    1. freelist 存储空闲块(object)；
    2. 生成很多freelist;每个freelist同一种大小空闲块；

2. 小对象如何分配;  
    1. 从本地线程 freelist 获取空闲块；
        > 最快；
    2. 本地不够； 从对应centralFreelist 获取几个可用的block; 加入本地 freelist;

    3. central也不够，从pageHeap获取N个span(一个span多个page构成)，span被拆分成N个空闲块，再返回本地freelist;

    4. 全局不够，直接向系统申请一大块；

3. 大对象如何分配;
    直接从large span 链表分配，一步到位；

4. 对象归还;
    1. free后重新加入本地freelist
    2. 当freelist长度超过就会归还object 给centralfreelist

### 3. go alloce

![lSY3xQ](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211130/lSY3xQ.jpg)

1. code

   ```
                                                                                    
   types span struct {
      startAddres
      allocBit 

   } 
   var spanDic map[class]span

   allocate(size int) *block{

      span := spanDict[size];
      for _, s :=  range s {
         if s.free = true {
            return  
         }
      }

   }


   go.machedict[]]

   ```

2. 多级分配

    span= object1->object2..: span 管理着内存
    1. porcessor.mcache.[span1,span2]; 无锁访问
    2. cneterlist.spanList;  locker
    3. heap;

## garbage collector

###  collect types

![okZlTV](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220124/okZlTV.jpg)



#### manual;

   ```
   int *p = alloce()
   *p = 100;
   free(p)
   ```

#### reference count:
1. what:
      relase if referenceCount==0;
2. language
      oc;python，php
3. how

      ```go  
      Objecx{value,count};
      var a = new Object(); // object.count += 1;
      var c = a; // object.count +=1
      if (object.count) > 0；free(object) 
      ```

4. pros：
      1. 回收及时;
5. crons:
      1. 额外空间和时间
      2. 循环引用问题；

#### tracing(mark-clean)
what: check object in  reference graph , relase if  object not in object graph
language: java, go;
how:  

      ```c
      object in root.refrencgraph = true;
      mark(object);
   
      clean(notmarked object)
      ```



4. simple  mark, sweep;

   ```go
   //  mark 
   func mark(root){  
      markBit(root) = true
      for  refer  in  references(root) {
          mark(refer)
       }
   }


   //  1. scan all allocated objects;
   //  2. relase unmark object
   func sweep(heapLink){
       for  allocedObjct in alloctedList { 
           if allocedObjct.isMark {
                  resetMark(block.object)  
               }else{
                  relese(block.object)
               }
           }
       }
   }

   ```

### sweep type

1. mark  sweep;
   ![zBOCno](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220124/zBOCno.jpg)
   sweep unmark;
2. mark  copy;
   ![Ze2qAj](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220124/Ze2qAj.jpg)
   copy mark;
3. mark  compact;
   ![B9yFfT](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220124/B9yFfT.jpg)
   copy mark and compact;

### mark type

1. naive mark;
   1. white
   2. black

2. tri-color
   1. white
   2. grep:
   3. black

## tri-color algorithm
%%TODO:%%

![3r56RT](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220125/3r56RT.jpg)


what:
three color   to mark object
1. white initital state
2. grey: be scanned but child not；middle state;
3. black: be scan 

why:
solve concurrency problem

gcroots:
1. stack object;
2. global object;;



mark phase: each processor start a work g to mark object
1. mark prepare
	1. start write barrier 
2. markinng
3. mark terminate  

### concurrency problem

what:
   break refrence graph, blockObjct.ref = whiteObjct; 

two invairant:
1. 黑色对象指向白色对象:  black.ref = white
2. 灰色对象到白色对象的访问路径被破坏: grey.ref != white 
	
      ![7iL7Nl](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220125/7iL7Nl.jpg)


```c
C.ref2 = A;
A.ref1 = B;

C mark black;
A mark grep;   

		  C.ref3= B;
		  A.ref1 = nil

A mark black;

//C is omitted;

```



### barrier 


what:
a hook in mutator function;
```go 
set(slot, newObj):
	slot = newPointer  //  slot:  old bject newobj:
	grey(slot) 
	grey(newObj)

// example: a1,a2 turn grey
a.a = a1
a.a = a2;
```


why it work:
the mutator break the graph,  so it should notify the collector; **who break, who are responsible**


### insertion barrier:
```q
  grey(newObj )
  *slot = newPtr
}      
```

   ![1VXdeb](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220223/1VXdeb.jpg)


probem:

1. need to rescan the stack again;

### deletion barrier:

what?
grey old obj 
   ```go
   setObject(slot, newObj){
      grey(oldOb)
      *slot = newObj
   ```


   ![BExc5O](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220223/BExc5O.jpg)

   ![OcIkiZ](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220223/OcIkiZ.jpg)

problem: 
1. 回收精度低, 不引用对象没有被及时回收

### hybrid barrier:
```c
setObjct(slot, newoObj):
	grey(slot)
	grey(newObj)

```


1. stack:  all stack object mark heap;  new  created obj in stack are makred black
2. heap hybrid barrier; 




####  case
case 1: 
![[Pasted image 20221107230907.png]]

![[Pasted image 20221107230920.png]]

case2:

## how to sweep
%%TODO:%%
1. code

   ```
   span.allocBits = span.gcmarkBits
   span.gcmarkBits = newMarkBits(s.nelems)
   ```

