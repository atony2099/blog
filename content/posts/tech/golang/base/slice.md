---
title: Slice
date: "2021-09-02T09:23:49+0800"
draft: false
categories: ["Go"]
---

[切片的容量是怎样增长的 | Go 程序员面试笔试宝典](https://golang.design/go-questions/slice/grow/)

##  basic operator 

create:

```go
 s := []int{1,2,3}
 s1 := make([]int, 2, 2)

fmt.Println(len(s1),cap(s1))
```

basic opereate

```go 
// append 
s = append(s,4)

// cut
s = s[0:1]


// max slice lenght=cap
s :=  make([]int, 3,4)
s = s[:] // [0,0,0]
s = s[:cap(s)] // [0,0,0,0]


```

展开
```go
s1 := s[0:1]
s2 := s[1:]

s = append(s1,s2...)

```


### empty slice vs nil slice

nil: 未初始化
empty: 已经初始化，创建了底层数组

how nil slice work: alloc  then work 





### vs array

1. size: dynamice vs fixed
array must have a fixed size when created, more efficent when size is known

```go
s := [...]int{1,2,3}
s1 := [3]int{1,2,3}
```

2. pass: value vs reference，new array won't affect origin array 

```go
s := [...]int{1, 2, 3}
arr1 := s
slice1 := s[:]
arr1[0] = 10
slice1[1] = 20

fmt.Println(s) // [1,20,3]
```




## how

underlying struct: 
```go
type  arrray  struct{
    len int
    cap int
    pointer *array;

}
```


### grow


cap容量足够:  简单的改变len就能实现
```go
var s = make([]int,5,10)
len(s) // 5
s = s[:10]
len(s) // 10
```


cap 容量不够: 增加cap

1. <  threshod = 256 || 1024:  双倍扩容
2. \>= threshod:   oldcap+(oldcap+3*256)/4 || 1.25 

```go
// go 1.18 src/runtime/slice.go:178
func growslice(et *_type, old slice, cap int) slice {
    // ……
    newcap := old.cap
	doublecap := newcap + newcap
	if cap > doublecap {
		newcap = cap
	} else {
		const threshold = 256
		if old.cap < threshold {
			newcap = doublecap
		} else {
			for 0 < newcap && newcap < cap {
                // Transition from growing 2x for small slices
				// to growing 1.25x for large slices. This formula
				// gives a smooth-ish transition between the two.
				newcap += (newcap + 3*threshold) / 4
			}
			if newcap <= 0 {
				newcap = cap
			}
		}
	}
	// ……
    
	capmem = roundupsize(uintptr(newcap) * ptrSize)
	newcap = int(capmem / ptrSize)
}
```



simple code

```go

func appendInt(s []int, list ...int) []int {
	originCap, originLength := cap(s), len(s)
	needCAP := len(s) + len(list)

	if needCAP > originCap {
		doubleCap := originCap * 2
		var newCap int
		if doubleCap < needCAP {
			newCap = needCAP
		} else {
			newCap = doubleCap
		}
		newSlice := make([]int, newCap)
		copy(newSlice, s)
		s = newSlice
	}

	// slice扩容一次
	s = s[:needCAP]

	copy(s[originLength:], list)
	return s

}


```




