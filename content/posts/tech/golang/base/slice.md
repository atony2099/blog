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

length := len(s)

```

展开
```go
s1 := s[0:1]
s2 := s[1:]

s = append(s1,s2...)

```



### how

### vs array

slice: dynamic array

1. dynamic size  and fixed size
2.  pass by value vs pss by reference: 数组不会对原数组产生影响
4.  in fixed size:  array more efficient 



### structure


struct: 
```go
type  arrray  struct{
    len int
    cap int
    pointer *array;

}
```

append:
1. 小于 cap,  在底层数组append 
2. 大于cap,扩容


grow:
1. < cap:
	1. increase slice length: `s = s[0:n]`
	2. underlying  array append


2.  > cap:
	1. get new capicity: double or 1.24;
	2. make new array; copy old value to new array
			`newS = make([]T,n,n); copy(newS: oldS);`





## other
### empty slice vs nil slice;
same: 
1. can append element,
2. len and cap  == 0 
different:
1.  nil slice:  underlying array is nil, 
2.  empty slice: underlying array is empty
3.  marshal: nil->{"a":null}, empty->{"a":[]}




