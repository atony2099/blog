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









```

展开
```go
s1 := s[0:1]
s2 := s[1:]

s = append(s1,s2...)

```


### empty slice vs nil slice

same:
 行为相同： 
 1. len, cap == 0, 
 2. can append   directly 

why same：
append时候都会调用 mallocgc来创建新的underlying array


different:
1.  nil slice:  underlying array is nil, 
2.  empty slice: underlying array is empty
3.  marshal: nil->{"a":null}, empty->{"a":[]}




### vs array

1. size: dynamice vs fixed
array must have a fixed size when created, more efficent when size is known

```go
s := [...]int{1,2,3}
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


how to grow：
<  threshod = 256 || 1024:  双倍扩容
\>= threshod:   oldcap+(oldcap+3*256)/4 || 1.25 

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




```




