---
title:  go base
date: "2021-02-28T09:17:11+0800"
categories: ["Go"]
---

[Go defer 原理和源码剖析](https://studygolang.com/articles/35316)

[The empty struct](https://dave.cheney.net/2014/03/25/the-empty-struct)

[deferx](https://github.com/cch123/golang-notes/blob/master/defer.md)

## type

basic:
1. int, int8,int32
2. float32,float34
3. string

composite  type:
1. container:
      1. array
      2. map
      3. slice
2. pointer
3.  channel;
4. struct;
5. interface
6. function 


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

 c be copy  to defer.struct ,  the copyed value is 0;

case 2: 
```go
func a1() (i int) { // return  302  
	defer func() {
		i += 300
	}()
	return i + 2 
}

func a2() int {   // return  3;
	var i int
	defer func() {
		i += 300
		fmt.Println(i)
	}()

	return i + 2
}
```

1. a1  add the return value
2. a2 add the local value 

## make&&new

some:
1. create variable
2. allocate memory for some type 


differ

1. initialize value
	1. make:   initialize(assgin a value),  **can use directly**
	2. new:  not initialize value,  the value then be assigned by zero value 

2. return 
	1. make: return reference
	2. new: return pointer


4. return value


make:
```go
make(map[int]int):  

make(chan int)

make([]int,5,10)

```

new  in go vs other
other:  allocated in heap(allocate dynamic memory) and return address
new: return address 

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


use case:   不需要实际变量的场景下, 作为一个占位符， 优化内存

1. channel  同步信号
2. 作为mthod(不需要更新状态)的实现类型 
3. map 实现 set 

```go
func addtoSets(a ...int) map[int]struct{} {
	m := make(map[int]struct{})
	for _, v := range a {
		m[v] = struct{}{}
	}

	return m
}

```




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



## comparable

what:
是否可以使用 ==；

### comparable,==

1. primitive type:same value
2.  pointer: same address 
3.  channel: same reference

```go
var a = make(chan int)
b := a
```

4. struct: if child filed are comparable 
5. interface: if actual type are comparable 


### uncomparable 

不能使用==，只能 reflect.DeepEqual 

1. function: both nil;
2. map: key and value are equal;
3. slice: index and value are equal;




### two  nil alway == ?
nil 的类型一致才相等 
```go
var p *int = nil
var i interface{} = nil
if p == i {
	fmt.Println("Equal")
}
```