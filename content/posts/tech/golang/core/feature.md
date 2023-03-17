---
title: feature
date: "2021-09-05T13:34:09+0800"
draft: false
categories: ["Go"]
---

[Type-Unsafe Pointers](https://go101.org/article/unsafe.html)


## 1. NO reference only pointer type
没有引用类型；使用指针作复合数据
特殊的指针:
   1.  No care  pointer cycleLife
        > garbage collector;
   2.  limit  the usage of pointer:
        1. No  pointer  convert;
        2. No  pointer arithmetic
        > use unsafe.Pointer 

### unsafe.Pointer

```go
package unsafe
type ArbitraryType int //表示任何类型
type Pointer *ArbitraryType //表示任何类型的指针
```

