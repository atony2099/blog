---
title: Slice
date: "2021-09-02T09:23:49+0800"
draft: false
categories: ["Go"]
---


## what 
1. what: dynamic array,  basic on array;
2. length and cap:
    length: dynamic array(slice) length;
    cap: static array length;

3. create:
   ```go
    // 1. literal
    var a = []int{1,2,3};
    // 2. make 
    var a1 = make([]int,1,3) // length,cap;
    
    // 3. cut from other slice;
    var a2 = a[:];
    var a3 = a[0:len(cap)]; // max cut length = len(cap)
     
   ```

## how 

struct: 
```go
type  arrray  struct{
    len int
    cap int
    pointer *array;

}
```

1. len: slice length
2. cap:  underlying array lengh;


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




