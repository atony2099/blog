
---
title: Go基础1, 函数 function
date: "2020-10-11T20:16:30+0800"
category: ["Go"]
lastmod: 2021-04-06T22:50:18+0800
---


函数是每门编程语言重要组成部分，golang作为一门现代化的语言，函数上有自己的一些特点



## 特点

### 1. 支持可变参数函数(variadic function)
如果一个函数的参数数量是可变的，则该函数是可变参数函数


Go中可变参数函数的最后一位参数使用 `参数名 ...类型`的格式声明，表明该类型参数可传入的数量是不限制的

```go
func function_name(para1 type,para2 type, para3 ...type){
// code...
}
```
要点：

1. 在函数内部 `...type`参数是用一个slice类型的变量表示
2. 可以用 `slice类型变量...`方式传入可变参数


```go
package main

import "fmt"

func sum(init int,nums ...int) {
    for _, num := range nums { // nums是slice 类型
        init += num
    }
    fmt.Println(total)
}

func main() {

	// 参数的数量可变
    sum(0,1, 2)  // 3 
    sum(0,1, 2, 3)  // f6

    nums := []int{1, 2, 3, 4}
    sum(nums...)  //  传入可变参数
}
```

### 2. 可以定义多个返回值并为返回值命名

函数返回多个值是开发中很常见的需求，然而大部分语言都不直接支持返回多个值，需程序员们另辟蹊径，通常解决方法是把多个值用对象或者数组包装起来，如 js 可以把多个返回值放在一个object中

```js
function getNames() {
    let firstName = 'John',
        lastName = 'Doe';
 
    return {
        firstName,
        lastName
    };
}
```
Go则在语言层面直接支持了这一特性
```go
package main
import "fmt"

func swap(x, y string) (string, string) {
	return y, x
}

func main() {
	a, b := swap("hello", "world")
	fmt.Println(a, b)
}
```

同时可以给返回值命名，从而使得函数更加清晰，可读性更强

```go
func RGBA() (r, g, b, a uint32) {
	.....
}

func nextInt(b []byte, pos int) (value, nextPos int) {
  ....
}
```





#### 1. naked return 

如果返回值已经命名，函数可以直接return不携带返回值,这种称为naked return(裸返回)， naked return在较短的函数中使用可以简化代码，但一般不建议在较长的函数内使

用，它会降低函数可读性

```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

```

#### 2. shadowed error

1. what？
	compile error;

2. when: 
   1. in child scope, return parameters is overwritten 2. naked return

	> return parameters is not at in the scope of return;

   ```go
   func Foo() (n int, err error) {
       if true {
           err := fmt.Errorf("Invalid") // err is shadowed during return
           return
       }
       return
   }

   ```







### 3. 支持一等函数（first class function)和高阶函数(high order function) 

一等函数, 即把函数看成和其他变量类型一样，允许把函数赋值给一个变量，允许把函数作为其他函数的参数和返回值。

如果一个函数的参数或者返回值包含另一个函数，则该函数是高阶函数。

```go
package main

import "fmt"

func sayHello() string {
	return "hello, "
}

func greeting(sayHello func() string, name string) { // sayHello 函数被当做greeting函数的一个参数
	fmt.Println(sayHello() + name)
}
func main() {
	greeting(sayHello, "go")  // hello, go
}

```

高阶函数的写法可以提高我们代码更加简洁优雅。如Go的排序函数Slice,通过传入less函数实现排序:

```go
func Slice(slice interface{}, less func(i, j int) bool) {
	rv := reflectValueOf(slice)
	swap := reflectSwapper(slice)
	length := rv.Len()
	quickSort_func(lessSwap{less, swap}, 0, length, maxDepth(length))
}
```

使用：

```go
package main

import (
	"fmt"
	"sort"
)

func main() {
	people := []struct {
		Name string
		Age  int
	}{
		{"Gopher", 7},
		{"Alice", 55},
		{"Vera", 24},
		{"Bob", 75},
	}
  
  
	sort.Slice(people, func(i, j int) bool { return people[i].Name < people[j].Name }) // 按照 Name排序
	fmt.Println("By name:", people) // By name: [{Alice 55} {Bob 75} {Gopher 7} {Vera 24}]

	sort.Slice(people, func(i, j int) bool { return people[i].Age < people[j].Age }) // 按照 Age 排序
	fmt.Println("By age:", people) // By age: [{Gopher 7} {Vera 24} {Alice 55} {Bob 75}]
}

```

#### 闭包(closure)

闭包，是由一个函数与它引用的外部变量构成,可以理解它是一个持有外部变量的特殊函数,闭包通常出现在高阶函数中

```go
package main

import "fmt"

func intSeq() func() int {
	i := 0
	return func() int { // 返回函数与i构成一个闭包
		i++
		return i
	}
}

func main() {

	nextInt := intSeq()
	fmt.Println(nextInt()) // 1
	fmt.Println(nextInt()) // 2
	fmt.Println(nextInt()) // 3
}
```

intSeq 函数返回一个闭包，该闭包持有i变量，在Go内部实际上是用一个结构体表示

```go
type Closure Struct {
	i *int
	f func()int
}

```



## 4. function 类型
```go
func xiao(name string){

}

type Person func (name string)


var x Person = xiaodong
```


每个function被声明的时候默认有个类型， 如上xiao默认是 func(name string)类型

也可以显示定义一种function类型；是否属于该函以参数和返回值作为依据；


## methods
1. what's 
	function+ data,light class
   1. have no constructor,inherit
   2. light access control: 
      1. public: upper case
      2. private: lower case




##  how it work

![wHiBKLn41izA](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230406/wHiBKLn41izA.jpg)

```go
func myFunction(a, b int) (int, int) {
    return a + b, a - b
}

func main() {
    myFunction(66, 77)
}
```


```assembly
"".main STEXT size=68 args=0x0 locals=0x28
    0x0000 00000 (main.go:7)    TEXT    "".main(SB), $40-0
    0x0000 00000 (main.go:7)    MOVQ    (TLS), CX
    0x0009 00009 (main.go:7)    CMPQ    SP, 16(CX)
    0x000d 00013 (main.go:7)    JLS    61
    0x000f 00015 (main.go:7)    SUBQ    $40, SP // 分配 40 字节栈空间
    0x0013 00019 (main.go:7)    MOVQ    BP, 32(SP) // 将基址指针存储到栈上
    0x0018 00024 (main.go:7)    LEAQ    32(SP), BP
    0x001d 00029 (main.go:8)    MOVQ    $66, (SP)  // 第一个参数
    0x0025 00037 (main.go:8)    MOVQ    $77, 8(SP) // 第二个参数
    0x002e 00046 (main.go:8)    PCDATA    $0, $0
    0x002e 00046 (main.go:8)    CALL    "".myFunction(SB)
    0x0033 00051 (main.go:9)    MOVQ    32(SP), BP
    0x0038 00056 (main.go:9)    ADDQ    $40, SP
    0x003c 00060 (main.go:9)    RET

"".myFunction STEXT nosplit size=49 args=0x20 locals=0x0
    0x0000 00000 (main.go:3)    TEXT    "".myFunction(SB), NOSPLIT, $0-32
    0x0000 00000 (main.go:3)    MOVQ    $0, "".~r2+24(SP) // 初始化第一个返回值
    0x0009 00009 (main.go:3)    MOVQ    $0, "".~r3+32(SP) // 初始化第二个返回值
    0x0012 00018 (main.go:4)    MOVQ    "".a+8(SP), AX    // AX = 66
    0x0017 00023 (main.go:4)    ADDQ    "".b+16(SP), AX   // AX = AX + 77 = 143
    0x001c 00028 (main.go:4)    MOVQ    AX, "".~r2+24(SP) // (24)SP = AX = 143
    0x0021 00033 (main.go:4)    MOVQ    "".a+8(SP), AX    // AX = 66
    0x0026 00038 (main.go:4)    SUBQ    "".b+16(SP), AX   // AX = AX - 77 = -11
    0x002b 00043 (main.go:4)    MOVQ    AX, "".~r3+32(SP) // (32)SP = AX = -11
    0x0030 00048 (main.go:4)    RET
```

pass parameter by stack(caller func's stack )

vs c： pass by register 

pros:
1. easy to implement 
2. easy to implement mutiple returned value

cons:
1.  high cost 
