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
- [ ]  如何类型的类型


Generics type in  go

类型的泛化,允许函数存在一种以上的类型：

如何实现：类型参数；

``` 
      
func [T any]()
```



类型约束：T + any/other....  可以简单理解为类型的类型，是所有可能类型



目的：
减少重复代买提高代码的复用； 


泛型的约束: 类型的类型，类型的集合
也可以包含interface 
```

type strings interfae {
	desc()
}

type interface a {
  int32 | int64 | strings
  
}
```

```

使用场景

use case:
1.  编写通用的函数:如排序，比较大小
2.  编写通用的对象(struct 和 方法)，如链表，队列；

