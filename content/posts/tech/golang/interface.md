---
title: "interface"
date: 2020-05-14T11:39:03+08:00
draft: false
---
[Interfaces Explained](https://www.alexedwards.net/blog/interfaces-explained)



## 1. what is 
1. is a type 
2. contain a set of method signature


## 2. used for

1. 引入一个中间层，调用方和实现方解耦
![](https://img.draveness.me/golang-interface.png)



## 3.type assertion  vs  type conversions

1. assertion:
   将interface 抓换为 具体类型
```go
var a interface{} = 100;
var aa,ok = a.(string)
```

2. conversions


## 4. compile time check


## 5. interface with pointer and  value

1. if methods receiver is value; then value and pointer implement the interface
> for pointer, it could get value by dereference; then copy it;

```go
type Duck interface{ Quack() }

type Cat struct{ Name string }

func (c Cat) Quack() {

	fmt.Println("memo")
	c.Name = "abc"
}
func main() {
	var d Duck = &Cat{"jim"} // 使用结构体初始化变量
	d.Quack()
	fmt.Println(d)

}

```

1. if methods receiver is pointer, then only pointer implement the interface

> for value receiver, it could not get origin pointer;

```go
type Duck interface{ Quack() }

type Cat struct{ Name string }

func (c *Cat) Quack() {

	fmt.Println("memo")
	c.Name = "abc"
}
func main() {
	var d Duck = Cat{"jim"} // 使用结构体初始化变量
	d.Quack()
	fmt.Println(d)
}

```





### todolist

1. interface with compile time

