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

basic operate:
```go 
// append 
s = append(s,4)

// cut
s = s[0:1]

//range
for index,value := range s {
		....
}

copy(slice1, slice2)


// max slice lenght=cap
s :=  make([]int, 3,4)
s = s[:] // [0,0,0]
s = s[:cap(s)] // [0,0,0,0]


```

inert at some Index:
```go
var position = 2;
var element = 100;  
var a = []int{1,3,4}

// func1
var inserts []int
inserts = append(inserts, a[:position])
inserts = append(inserts,element)
inserts = append(inserts,a[position:])


// func2
a= append(a,0)
// move
copy(a[position+1:],a[position:])
a[position]=element


```

展开
```go
s1 := s[0:1]
s2 := s[1:]

s = append(s1,s2...)

```

### empty slice vs nil slice

nil: 未初始化， 但是可以直接使，因为go自动初始化了
empty: 已经初始化，创建了底层数组


### vs array

array: fixed array，value type
slice: dynamic array, reference type 


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

![oqSAmTMTVY8p](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230711/oqSAmTMTVY8p.jpg)


len: slice当前长度； 已使用容量
cap: 
1. slice可以使用的容量
2.  裁切的上限 

underlying struct: 
```go
type  arrray  struct{ 
    len int  // slice长度
    cap int //  可用容量 
    pointer *array; // 底层数组

}
```

### grow


how:
1. 低于容量，直接append
2. 高于容量，先扩容，后append


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



simple code:

```
copy(slice[originLen: needLen], toBeCopyslice)
```

```go

func append(origin []int, new ...int) []int {

	if len(origin)+len(new) > cap(origin) {
		newSlice := make([]int, 2*(len(origin)+len(new)))
		copy(newSlice, origin)
		origin = newSlice
	}
	origin = origin[:len(origin)+len(new)]
	copy(origin[len(origin)-len(new):], new)
	return origin

}

```




