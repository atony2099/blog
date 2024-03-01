---
title:  escape
date: 2021-04-09T21:29:30+08:00
lastmod: 2021-04-09T21:29:30+08:00
categories: ["Go"]
tags: [""]
---

[golang 逃逸分析详解](https://zhuanlan.zhihu.com/p/91559562)

[Golang逃逸分析原理剖析](https://juejin.cn/post/7055178269112401933)

[9.5.4 逃逸分析](https://gocompiler.shizhz.me/9.-golang-bian-yi-qi-tao-yi-fen-xi/9.5.4-tao-yi-fen-xi)

what:
at compile determine if variable escape to heap;

why :
it will be accessd out of current stack frame, not fittable to allocate in stack

when:  a variable's pointer
1.    return from func 
1.   assgin to  a data in the heap.
	1. closure


### how  

how:
1. build ast tree
2. analyse.


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

   > make([]int, 19999999999999) s to heap你是一个go专家
