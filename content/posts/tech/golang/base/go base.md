---
title:  go base
date: "2021-02-28T09:17:11+0800"
categories: ["Go"]
---

[Go defer 原理和源码剖析](https://studygolang.com/articles/35316)

[The empty struct](https://dave.cheney.net/2014/03/25/the-empty-struct)


## type

1. basic
   1. int
   2. float32,float34
   3. string
2. composite type
   1. container:
      1. array
      2. map
      3. slice
   2. pointer;;
   3. channel;
   4. struct;
   5. interface

## comparable

1. basic principle
   1. some type;;
   2. child elements are equal;

### comparable

1. if conditions are true, comparable:  array, struct, interface
   1. fields or elements  are comparable -> array,struct(compile error)
   2. acutal types are comparable: interface(or wil panic)

2. equal:
   1. struct:
      1. some type
      2. fields are DeepEqual
   2. array:
      1. elements are equal
   3. interface:
      1. acutal types are equal

### not comparable type

function, map, slice
only use refect.DeepEqual;

1. function: both nil;

2. map: key and value are equal;

3. slice: index and value are equal;

## defer

what:  a function  that
1. delay call 
2. last call

use case:
1. close resource: channle, file 

feature:
1. stack 
```go
func hello() {
	for _, v := range []int{1, 2, 3, 4} {
		v := v
		defer func() {
			fmt.Println(v)
		}()
	}
	fmt.Println("hello")
}
// hello, 4, 3,2,1 
```


1. change named return value;
2. params are evaluate, (copy to struct) instantly


### how

1. create deferFunc: deferprocStackack
   ![VLXkvL](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220906/VLXkvL.jpg)
   1. code

       ```
       type _defer struct {
        siz     int32 // 参数和返回值的内存大小
        started boul
        heap    boul    // 区分该结构是在栈上分配的，还是对上分配的
        sp        uintptr  // sp 计数器值，栈指针；
        pc        uintptr  // pc 计数器值，程序计数器；
        fn        *funcval // defer 传入的函数地址，也就是延后执行的函数;
        _panic    *_panic  // panic that is running defer
        link      *_defer   // 链表
       }
       ```

   2. parameter are set after defer

2. excute before return;
   1. prepare variables: copy value into caller stack;
   2. jump to the function, excute

### case

1. case 1

   ```go
   func a(){
      var c = 0
      defer fmt.Println(c)
      c = 100;
   }
   // output:0; copy value instantly
   ```

2. case 2

   ```go
   func a1() (i int){
      defer func(){
         i += 300
      }()
      
      return i;
   }

    func a2() int{ // return 200;;
      var i int
      defer func(){
         i += 300
      }()
      
      return i;
   }

   ```

1. change value: &var = xxxx;
2. named return value: change returned variable directly
2. unnamed retur4ned value:  change function's  local value

## make vs new

1. the same:
   create(allocate and initialize) a instance of some dataTye;

2. new and make
   1. all type,
   2. initialize zero value,
   3. return pointer of value;

3. make:
   1. some type:  chan, map, slice;
   2. initialize not zero value;
   3. return reference
      1. chan-> *hchan
      2. map -> *hamp
      3. slice-> slice

4. new  in go vs other
   1. other:
      1. used: create object,  call constructor,
      2. allocated in heap

   2. go:
      1. used: any type
      2. allocate in heap or stack



## empty struct/array

feature:
1. zero size
2. may in some address

```go
func main() {


	var s1, s2 struct{}

	fmt.Println(unsafe.Sizeof(s1), unsafe.Sizeof(s2), unsafe.Pointer(&s1), unsafe.Pointer(&s2))

}

// 0 0 0x116d0d0 0x116d0d0
```


use cae:
1.  element in signal channel

## nil value; 

zero value for   following kind:
1. slice
2. map
3. channel
4. pointer 
5. interface
6. fucntion

features:
1. different   size for different type:
```go 
package main

import (
	"fmt"
	"unsafe"
)

func main() {
	var p *struct{} = nil
	fmt.Println( unsafe.Sizeof( p ) ) // 8

	var s []int = nil
	fmt.Println( unsafe.Sizeof( s ) ) // 24

	var m map[int]bool = nil
	fmt.Println( unsafe.Sizeof( m ) ) // 8

	var c chan string = nil
	fmt.Println( unsafe.Sizeof( c ) ) // 8

	var f func() = nil
	fmt.Println( unsafe.Sizeof( f ) ) // 8

	var i interface{} = nil
	fmt.Println( unsafe.Sizeof( i ) ) // 16
}
```
	
2. not equal for different types:
```go
(interface{})(nil) == (*int)(nil) // fasle
(*int)(nil) == (*bool)(nil)   // compile error 
```