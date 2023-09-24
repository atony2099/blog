---
title:  error
date: 2023-03-17T11:21:54+08:00
lastmod: 2023-03-17T11:21:54+08:00
categories: ["golang"]
tags: ["error"]
---

[Golang 错误处理和日志打印的 5 点建议 - https://blog.hackerpie.com/posts/programming-paradigm/golang-error-logs/](https://blog.hackerpie.com/posts/programming-paradigm/golang-error-logs/)

[Working with Errors in Go 1.13 - The Go Programming Language - https://go.dev/blog/go1.13-errors](https://go.dev/blog/go1.13-errors)


[golang错误处理最佳实践 | 余温旧梦-王世彪的博客 - https://www.sofineday.com/golang-err.html#%E4%B8%80-%E5%B8%B8%E7%94%A8%E7%9A%84%E9%94%99%E8%AF%AF%E5%8C%85%E8%A3%85%E6%96%B9%E6%B3%95](https://www.sofineday.com/golang-err.html#%E4%B8%80-%E5%B8%B8%E7%94%A8%E7%9A%84%E9%94%99%E8%AF%AF%E5%8C%85%E8%A3%85%E6%96%B9%E6%B3%95)

[3种方式！Go Error处理佳实践 - 文章详情 - https://z.itpub.net/article/detail/7F1B2EB3C5F2E6F989C2E0B9B9C183E8](https://z.itpub.net/article/detail/7F1B2EB3C5F2E6F989C2E0B9B9C183E8)

[Go错误处理：错误链使用指南 | Tony Bai - https://tonybai.com/2023/05/14/a-guide-of-using-go-error-chain/](https://tonybai.com/2023/05/14/a-guide-of-using-go-error-chain/)

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
##  最佳实践 
什么是好的错误处理:  


core:
1. 清晰的打印出错误链路
2. 拿到根本的原因，根据根本原因不同分别进行处理 



other:
1. 减少开发记录成本
2. 减少对外部错误的依赖
3. 区分系统错误和一般业务错误


如何做:
1. 使用 fmt.Errorf("xxx%w",err)，向上抛出错误,并注入本function 的信息: 逻辑堆栈
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

其他：
1. 减少对其他包错误的依赖， 转换为自定义的错误；  
2. 缺乏呢







