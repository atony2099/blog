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
- [x] interface 和  any 的关系
- [x]  类型约束 
	- [x]   是什么
	- [x] 可以包含intercace methods 吗
	- [x]  union ;
	- [ ] 常用的 interface
	- [ ] ~

- [ ]  类型推断;

Generics type in  go

类型的泛化,允许函数存在一种以上的类型：

如何实现：类型参数；

``` 
      
func [T any]()

```




目的：
减少重复代买提高代码的复用； 

## 类型 的约束:  type  constraint; type parameters

类型的类型，类型的集合
>  类型的本质就是约束；

how :
```
// 类型的类型
type  A interface  {
	type1 | type2 | type3
}

func go[A AType](b)
```


可以包含interface methods吗？
目前不能，因为类型集合如果有 interface 又有 type ， 你最终还是要通过反射来推断他的类型


```
func a: ~string | fmt.Stringer

func a:
swich a.(type)：
	string:
     fmt.Stringer:
     

```


~ type :  类型或者底层类型类 type 

```
type aa int 

~int: int and aa 
```


常用  constraints

```
type Ordered interface {
	Integer | Float | ~string
}

type Ordered interface {
	xxxx // can use == and != 
}
```

## 使用场景

use case:
1.  编写通用的函数:如排序，比较大小
2.  编写通用的对象(struct 和 方法)，如链表，队列；



