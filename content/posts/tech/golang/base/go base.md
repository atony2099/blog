---
title:  go base
date: "2021-02-28T09:17:11+0800"
categories: ["Go"]
---

[Go defer 原理和源码剖析](https://studygolang.com/articles/35316)

[The empty struct](https://dave.cheney.net/2014/03/25/the-empty-struct)
[defer](https://github.com/cch123/golang-notes/blob/master/defer.md)

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
1. close resource: channle, file, context 

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
![VLXkvL](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220906/VLXkvL.jpg)

structure: in goroutinue.deferlinklist 
```go
type _defer struct {
	siz     int32 // 参数和返回值的内存大小
	started bool
	heap    bool     // 区分该结构是在栈上分配的，还是对上分配的
	sp      uintptr  // sp 计数器值，栈指针；
	pc      uintptr  // pc 计数器值，程序计数器；
	fn      *funcval // defer 传入的函数地址，也就是延后执行的函数;
	_panic  *_panic  // panic that is running defer
	link    *_defer  // 链表
}
	
```

create defer:
1. copy parameter
2. push in go.defer
```go

func deferproc(siz int32, fn *funcval) { // arguments of fn follow fn
    sp := getcallersp(unsafe.Pointer(&siz))
    argp := uintptr(unsafe.Pointer(&fn)) + unsafe.Sizeof(fn)
    callerpc := getcallerpc() // 存储的是 caller 中，call deferproc 的下一条指令的地址

    d := newdefer(siz)
    if d._panic != nil {
        throw("deferproc: d.panic != nil after newdefer")
    }
    d.fn = fn
    d.pc = callerpc
    d.sp = sp
    switch siz {
    case 0:
        // Do nothing.
    case sys.PtrSize:
        *(*uintptr)(deferArgs(d)) = *(*uintptr)(unsafe.Pointer(argp))
    default:
        memmove(deferArgs(d), unsafe.Pointer(argp), uintptr(siz))
    }

    return0()
    // No code can go here - the C return register has
    // been set and must not be clobbered.
}

```

excute defer:   
1. check g in current func.
2.  excute defer 


```go
func deferreturn(arg0 uintptr) {
    gp := getg()
    // 获取到最前的 _defer 节点
    d := gp._defer
    // 函数递归终止条件（d 链表遍历完成）
    if d == nil {
        return
    }
    // 获取 caller 函数的 rsp 寄存器值
    sp := getcallersp()
    if d.sp != sp {
        // 如果 _defer.sp 和 caller 的 sp 值不一致，那么直接返回；
        // 因为，就说明这个 _defer 结构不是在该 caller 函数注册的  
        return
    }

    switch d.siz {
    case 0:
        // Do nothing.
    case sys.PtrSize:
        *(*uintptr)(unsafe.Pointer(&arg0)) = *(*uintptr)(deferArgs(d))
    default:
        memmove(unsafe.Pointer(&arg0), deferArgs(d), uintptr(d.siz))
    }
    // 获取到延迟回调函数地址
    fn := d.fn
    d.fn = nil
    // 把当前 _defer 节点从链表中摘除
    gp._defer = d.link
    // 释放 _defer 内存（主要是堆上才会需要处理，栈上的随着函数执行完，栈收缩就回收了）
    freedefer(d)
    // 执行延迟回调函数
    jmpdefer(fn, uintptr(unsafe.Pointer(&arg0)))
}

```


### explain why 

case 1:
   ```go
   func a(){
      var c = 0
      defer fmt.Println(c)
      c = 100;
   }
   // output:0; copy value instantly
   ```

case 2
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