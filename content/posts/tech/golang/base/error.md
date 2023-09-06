
---

title: error
date: 2023-01-05
description:
tags: ["error"]
draft: false
---

[Effective Error Handling in Golang - Earthly Blog - https://earthly.dev/blog/golang-errors/](https://earthly.dev/blog/golang-errors/)
[Golang Error Handling — Best Practice in 2020 | by Che Dan | ITNEXT - https://itnext.io/golang-error-handling-best-practice-a36f47b0b94c](https://itnext.io/golang-error-handling-best-practice-a36f47b0b94c)


## error handle

how go handle error:
```go
f, err := os.OpenFile("test1.txt", os.O_RDONLY, 0666)

if err != nil {

fmt.Println("open file error")

}


defer f.Close()


type errorString struct {
	s string
}
func (e *errorString) Error() string {
	return e.s
}
```

erorr in go:  
1. 只包含简单的错误信息, 
2. 捕获: 通过 if 判断, 一个一个处理

error in another language:
1. 包含堆栈信息，错误信息
2. 捕获: 通过try...catch，批量处理

why go different with other language:
1. try..catch批量处理可能产生bug
```
try {
	inertA()
	insertB()
	insertC()
} catch {
	print(insert error)
}

1. insertA() erorr,print 
2. insertB() error, rollback InsertA,then print 
```

1. go 鼓励开发者面对每个error，解决每个错误，越简单越明确越不容易产生bug


什么是好的 错误信息机制 :
1. 及时明确: 发现错误，立即处理
2. 错误信息越丰富越好
3. 能识别不同错误类型，做不同的处理

the error  problem in go:

1. 无法获得完整调用链路: 使用 `fmt.Errorf("some thing error %v",errr)`
	1. 每一层都加信息，至少添加function名， 完整链路
2.  层级较深的调用， 在上层需要根据底层错误做不同响应: 使用  
1. 过多重复的处理，如每次error 都 print.  解决: 重复 的log 就在顶层做统一处理




## wrap

wrap: 在丰富错误信息的可以保留原始的error, 从而获取完整调用链路
```go
type wrapError struct {
	msg string
	err error
}

func wrap(msg string,  err error) error {
	return   wrapError{
		msg:  msg + err.Error,
		err : err
	}
}

func (w *wrapErrro)Unwrap() error{
		return w.err
}


func Is(err,err2) {
	
}


//  check err is  type of something 
var p *Error
func As(err,&p){
	value, ok := err.(p.Tye);
	if ok:
		return value
	else
		return As(err.err, &p)
}

```




