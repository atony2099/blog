---
title: closure
date: "2021-08-29T17:47:16+0800"
categories: ["Go"]
lastmod: 2023-04-08T00:26:33+0800
---

what: 一个函数 访问(引用)了父函数作用域的 变量
```go
func outer() func() int {
	var counter = 0
	return func() int {
		counter++
		return counter
	}
}
```
feature: 外部变量在作用域外还继续存在.


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


## explain why

```go
// case1
func closure() {
	for i := 0; i < 10; i++ {
		go func() {
			println(i)
		}()
	}
}

// go func 引用外部变量，是一个闭包； 
// i的值 是闭包执行的时候 
//

// case2
func closure2() {
	for i := 0; i < 10; i++ {
		i := i
		go func() {
			println(i)
		}()
	}
}
```


case1: 未知 
1. i 同时被 10个闭包引用,闭包执行时机不确定，可能在for 循环中,可能在for 循环中，结果不可预测
2. 如果是 signle cpu, go 在 for循环后才被执行， 输出是10

case 2: 乱序输出 (0,9)
1. 闭包 引用是本地变量i,每个闭包看到的值是不一样

