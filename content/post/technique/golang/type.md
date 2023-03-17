
---
title: Golang 中的类型
date: 2020-05-14T11:39:03+08:00
draft: false
---


## 类型分类

### 1. 值类型和指针类型

1. 值类型: 变量之间传递的的是值的拷贝
2. 指针类型: 变量之间传递的是指针的拷贝







### 1. 基础类型

```go
int
float32, float64
bool
string
```

### 2.复合类型

1. container 
   1. arrary
   2. slice 
   3. map

2. struct 
3. pointer 
4. channel
5. function
6. interface 



## 3. 类型的声明（type declaration）

### 1. type definitions vs type alias
```go
// 1. basic type 
type A int
// 2. struct 
type Bird struct {

}
// 3. interface 
type BirdInterface interface {

}
```

#### 2. type alias
```go 
type Name = int 

```



#### 3. definition vs alias
1. definition:
    1. create a new type  
2. alias
   1. not create a new type


use case:
[Representing enums in go](https://dev.to/shiraazm/representing-enums-in-go-21dp)

1. enum

```go
type Suite int

const (
  unknown Suite = iota
  Spades
  Hearts
  Diamond
  Clubs
  sentinel
)

func (s Suite) isValid() bool {
  return  s > unknown && s < sentinel
}
```


2. add function

```go
type Person string
func (p Person) Hello(anotherPerson Person) {
    fmt.Printf("%s, Hello from %s", anotherPerson, p)
}
elon := Person("Elon Musk")
aleh := Person("Aleh")
elon.Hello(aleh) // Aleh, Hello from Elon Musk

```




