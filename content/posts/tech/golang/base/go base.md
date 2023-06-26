---
title: basic
date: "2021-02-28T09:17:11+0800"
categories: ["Go"]
---

[后端 - Go defer 原理和源码剖析 - 个人文章 - SegmentFault 思否 - https://segmentfault.com/a/1190000040950441](https://segmentfault.com/a/1190000040950441)

[The empty struct](https://dave.cheney.net/2014/03/25/the-empty-struct)

[defer](https://github.com/cch123/golang-notes/blob/master/defer.md)

## type


form basic  or not:
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

### value type and reference type.

value type: 存储和传递实际值
reference type: 存储和传递值的引用(pointer)

value type: 
- basic type
	-   Numeric types:
	    -   `int`, `int8`, `int16`, `int32`, `int64`
	    -   `uint`, `uint8`, `uint16`, `uint32`, `uint64`, `uintptr`
	    -   `float32`, `float64`
	    -   `complex64`, `complex128`
	-   Boolean type: `bool`
	-   String type: `string`
-   Array type: `[]T`
-   Struct type: `struct{}`


reference type: 
-   Slice type: `[]T`
-   Map type: `map[K]V`
-   Channel type: `chan T`
-   Function type: `func(params) returnType`

pointer   type

### pass by value


pass by value: 传递的是变量的值
pass by reference:   传递的是变量的引用


go  is pass by value:
1. 对于值类型,  传递的是值本身: value  
2. 对于引用类型， 传递的是引用值， reference value






## defer

what:  a function  that last call

use case:
1. close resource: channle, file, context 

feature:
1. stack 
2. 立即传递参数
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



### how

how:
1. create defer: deferproc 
2. excute defer: deferreturn


```
// 内存布局 
[ defer struct   ][arg1，arg2,arg3....  ]
```



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

func newdefer(siz int32) *_defer {
    var d *_defer
    sc := deferclass(uintptr(siz))
    gp := getg()
    if sc < uintptr(len(p{}.deferpool)) {
        // 从 p 结构体的 deferpool 中获取可用的 defer struct
        // 代码比较简单，省略
    }
    if d == nil {
        // 上面没有成功获取到可用的 defer struct
        // 因此需要切换到 g0 生成新的 defer struct
        systemstack(func() {
            total := roundupsize(totaldefersize(uintptr(siz)))
            d = (*_defer)(mallocgc(total, deferType, true))
        })
    }
    // defer func 的参数大小
    d.siz = siz
    // 链表链接
    // 后 defer 的在前，类似一个栈结构
    d.link = gp._defer
    // 修改当前 g 的 defer 结构体，指向新的 defer struct
    gp._defer = d
    return d
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

case:  stack
```go
func main() {
	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}
	fmt.Println("done")
}
// output: done, 9...1 
```


case 2: 立即拷贝参数
   ```go
   func a(){
      var c = 0
      defer fmt.Println(c)
      c = 100;
   }
   // output:0; copy value instantly
   ```



case3: 

1. 闭包,可以直接修改引用对象的值
2. f1 修改的的是本地变量， f2直接修改返回值 

```go
func f1 () (r int) {
    t := 1
    defer func() {
        t = t + 5
    }()
    return t
}


func f3() (r int) {
    defer func () {
        r = r + 5
    } ()
    return 1
}
```


## make&&new
make: 
1. 申请内存，初始化:
2. 返回引用
3. used for: map, slice, chan

new:
1. 申请内存，未初始化
2. 返回指针 
3. used for all types



make:
```go
make(map[int]int):  

make(chan int)

make([]int,5,10)

```



## empty struct/array

what:  zero field

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




## zero  value

what:  一个特殊的value,  如果没有被初始化，则会被赋予的值

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
是否可以使用 \==；


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

不能使用 \==(除了 nil)，只能 reflect.DeepEqual 

1. function: == nil;
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




## pass by value  


reference vs pointer:
reference:  safer pointer, auto address and dereference


pass by value: 传递(复制)的变量的值
pass by reference: 传递变量的地址

```go


func add(num int){
	num += 1
}
var a = 100
add(a)
fmt.Println(a)




func addRef(num &int){
	num+=1
}
var a = 100
addRef(a)
fmt.Printlin(a)//101

// 
int* num = &a
*num+=1
```

