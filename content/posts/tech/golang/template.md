---
title:  error
date: 2023-03-17T11:21:54+08:00
lastmod: 2023-03-17T11:21:54+08:00
categories: ["golang"]
tags: ["error"]
---


- [ ] 是什么？如何处理
- [ ] 如何处理
- [ ] 最贱实践
	- [ ] 获取



- [ ] 业务开发中的错误处理





## go 中的error 

```
type errorString struct  {
	s string 
}


func (e *errorString)Error()string {
	return e.s
}


```



## go 中error 的处理

是什么:


