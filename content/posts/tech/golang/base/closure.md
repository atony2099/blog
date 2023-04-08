---
title: closure
date: "2021-08-29T17:47:16+0800"
categories: ["Go"]
lastmod: 2023-04-08T00:26:33+0800
---


```go
func outer() func() int {
	var counter = 0
	return func() int {
		counter++
		return counter
	}
}
```
what: 一个函数 访问(引用)了父函数变量,  在父函数返回后还能继续访问这变量 

why call closure: 将外部变量关闭在本函数 

pros:
1.  a function can remember some satate between   calls

vs  anonymous  function:   
1. anonymous func: have no name
2. closure: 在go, closure 都是以anonymous的形式存在，

how: create  a   closure    
 ```go
type Closure struct {
	F uintptr
	X *int
}
```
why varaible  keep when function return:
和 closure 一起逃逸到heap 上


2. when?
   1. anonymous  func access parent scope variable;
   2. anonymous  func called delay



## explain why

```go
func closure() {
	for i := 0; i < 10; i++ {
		go func() {
			println(i)
		}()
	}
}
```

go func  is closure， 
1. one cpu: g 10
2. mutiple cpu, g 在父函数还在运行过程中


```go
func main() {
	for i := 0; i < 10; i++ {
		var q = i
		go func() {
			println(q)
		}()
	}
	time.Sleep(time.Second)
}

func main() {
	for i := 0; i < 10; i++ {
		go func(i int) {
			println(i)
		}(i)
	}
	time.Sleep(time.Second)
}

```


2. case 1:
    1. every go func is a closure, closure reference i;
    2. go调度时机不确定:
    3. single processor: 10,10
         1. for 执行完其他g 再调度: 10,10,
         2. mutiple core: 1-10任意的数;

3. case 2:
    : closureList: {*q1,*funcA}, {*q2,*funcA}...

## 

### 1. counter

```go
package main

import (
 "fmt"
)

func counter() func() int {
 a := 0
 return func() int {
  a++
  return a
 }

}
func main() {
 c := counter()
 fmt.Println(c())
 fmt.Println(c())
 fmt.Println(c())
}
```
