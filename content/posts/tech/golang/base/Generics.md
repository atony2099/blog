---
title: Generics
date: 2024-01-07T21:19:09+08:00
lastmod: 2024-01-07T21:19:09+08:00
categories:
  - base
tags:
  - go
---


- [x] 是什么; 
- [x] 如何使用; 
	- [x] 函数；
	- [x] 类型 
- [ ] interface 和  any 的关系
- [ ]  如何定义 

## wha

1.  变量的类型不需要 明确， 只需要 规定一个范围
2. 使用变量的时候指定 类型的具体 
3. 可以理解为类型又有一个类型 ；

目的：
减少重复代买提高代码的复用； 



## how to
types:

1. 函数： 类型参数
2. 泛型类型:(实际还是作为函数的类型参数 )


```
func a[T any] (a T):
func b[T float32 | float64] (b T):

```


```
type Stack[T any] struct {

data []T
push func(T)
pop func() T

}
```



如何定义 类型的类型 

1. any , 实际是 interface{};  可以任何类型 