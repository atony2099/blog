---
title:  error
date: 2023-03-17T11:21:54+08:00
lastmod: 2023-03-17T11:21:54+08:00
categories: ["golang"]
tags: ["error"]
---


- [x] 是什么？
	- [x] what
	- [ ] vs other
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


what: 只是一个简单的string；信息太简单 

vs  try...catch:
1. 无法批量处理：打断业务流程
2. 原始错误只有一种类型， 不好分类处理
3.  无法打印出堆栈 

## 业务开发的问题 如何解决 


1. 清晰获取完整的错误链路
2. 获取根本原因: root errror


错误处理的基本原则；
1. a error should be hanle onece



1.  获取完整的调用链路；
2.  



1. 打印太多 error, 难以查看
2. 获取详细的调用链路
3. 无法获取根本的原因
    





1. 得到完整的调用链路: 使用逻辑 
```
fmt.Errorf("handle error:")
```


2. 处理不同类型错误 


