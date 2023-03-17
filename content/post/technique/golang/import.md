---
title: Go的空白标识符
date: "2021-01-16T18:13:00+0800"
draft: false
categories: ["Go"]
---


## 是什么?

_代表 被忽略的变量
underscore_ means  ignoring  the value;

## use case

1. 多个返回值忽略错误值

```go

if _, isExists := ageMaps["jim"];isExists{
 // do other thing

 }
```

2. 引入副作用(side effects)

the import package is  ignored, but the init function have taken effect;

```go

import (
 _ "github.com/go-sql-driver/mysql"
)
func init() {
 sql.Register("mysql", &MySQLDriver{})
}

```

1. 检测是否实现接口

```go
var _ DogType = (*BullDog)(nil)
```
