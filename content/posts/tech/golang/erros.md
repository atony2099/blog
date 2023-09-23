---
title:  error
date: 2023-03-17T11:21:54+08:00
lastmod: 2023-03-17T11:21:54+08:00
categories: ["golang"]
tags: ["error"]
---


- [x] 是什么？
	- [x] what
	- [x] vs other
	- [ ] sential error 
- [ ] 什么是好的错误处理，以及如何  
	- [ ] 



- [x] 基础概念
	- [x] 是什么？
	- [ ] vs other? feature 

- [ ]  最佳实践
	- [ ] 要求 
	- [ ] 如何做







## go 中的error 

```
type errorString struct  {
	s string 
}


func (e *errorString)Error()string {
	return e.s
}


type  error interfeace {
	Error() string 
}

```
a string, 返回最基本的信息； 



error type:

1.  作为返回值
2.  作为 try....catch 一部分, 编译器参与 



vs  try...catch:
pros:
1. 无法批量处理：打断业务流程
2. 原始错误只有一种类型， 不好分类处理
3.  无法打印出堆栈 
4. 开发者，繁琐 

cons:
1. 迫使开发者处理每一个错误，避免统一处理产生的bug
## best practice

什么是好的错误处理 
1. 获取完整调用链路，
2.  获得原始的错误类型  
3. 减少开发记录成本 


如何做:
1. 使用 fmt.Errorf("xxx%w",err)，向上抛出错误,并注入本function 的信息  
2. 只在最顶层做打印  
3. 使用 error.is error as获取 原始的信息 


```

func queryDB()error{
	return errors.New("db error")
}


func query() eror{
  err := queryDB(); err != nil{
	 return fmt.Errof("query error; cause:%"w)
  }
}

func main(){
	errr:= query();
	fmt.Println(error)
}

```