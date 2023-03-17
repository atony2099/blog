---
title: 短变量声明
date: "2020-09-30T14:10:34+0800"
draft: false
category: ["golang"]
---

## 是什么

`hello :="hello"`

短变量声明(short variable declaration)是golang中声明变量的一种方式,是golang特有的语法，相比 `var hello="hello"`的声明方式，可以少写一个 `var`，能稍微减少代码量

## 特点

### 1. 可以重复声明

一般强类型语言的变量都不允许重复声明，在golang中使用var重复声明变量同样会抛出错误

```go
package main
import "fmt"
func main() {
 var a = 100
 var a = 200
 fmt.Println(a)
}
```

编译时报错:`a redeclared in this block`

使用短变量声明时候:

```go
// example1:
package main
import "fmt"
func main() {
 a:= 100

 a := 200
 fmt.Println(a) 
}
// 编译时报错： no new variables on left side of :=


// example2: 
package main
import "fmt"
func main() {
 a, c := 100, 200
 fmt.Println(&a) // 0xc000014090
 a, d := 200, 300
 fmt.Println(a, c, d) // 100,200,300
 fmt.Println(&a)      // 0xc000014090
}
// 编译通过
```

example1 和  example2 中 变量a都被声明了两次，但只有example2可以编译通过。可以看到重复声明有效的前提是声明语句左边至少有一个是新变量。

**同时特别注意，被重复声明的变量并没有创建多次， 只是将新的值赋予原来的变量**（ [it just assigns a new value to the original](https://golang.org/ref/spec#Short_variable_declarations) ）。example2 中 变量a的内存地址始终是同一个

同时还要注意只有在同一作用域才是重复声明，如果在不同作用域声明相同变量名，则是创建新的变量

```go
package main
import "fmt"

func main() { // 作用域1
 a, b := 100, 200 
 if true { // 作用域2
  a, c := 300, 400
  fmt.Println(a, c)  // 300,400
 }
 fmt.Println(a, b) // 100,200 

```

上述 变量a 在两个不同作用域，是完全两个不同的变量

### 2. 只能声明在局部作用域内

不能声明在全局作用域，只能声明在局部作用域，包括函数内部，if,for,switch 语句中

```go
func main() {
 for i := 0; i < 100; i++ {
  fmt.Println(i)
 }
  
 if ok := true; ok {
   fmt.Println(ok)
  }
}
```

参考:
[Short variable declarations](https://golang.org/ref/spec#Short_variable_declarations)
