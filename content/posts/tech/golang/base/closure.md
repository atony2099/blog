---
title: closure
date: "2021-08-29T17:47:16+0800"
draft: false
categories: ["Go"]
---


## 1.what

1. a special object: function + refernece of  parent scope variable;

    ```c
    struct A{
        func *FUNC
        variable1 *Type 
       variable2 *Type

    }
    ```

2. when?
   1. anonymous  func access parent scope variable;
   2. anonymous  func called delay

3. vs anonymous function:
   closure usually implemented by anonymous function

## 2. analyse

1. code:

    ```go
     func main() {
        for i := 0; i < 10; i++ {
         go func() {
          println(i)
         }()
        }
        time.Sleep(time.Second)
     }

     // case 2
       func main() {
           for i := 0; i < 10; i++ {
           var q = i
            go func() {
             println(q)
            }()
           }
           time.Sleep(time.Second)
       }

   // case 3
     func main() {
        for i := 0; i < 10; i++ {
         go func(i int) {
          println(i)
         }(i)
        }
        time.Sleep(time.Second)
    }

    ```

2. case 1:
    1. every go func is a closure, closure reference i;
    2. go调度时机不确定:
    3. single processor: 10,10
         1. for 执行完其他g 再调度: 10,10,
         2. mutiple core: 1-10任意的数;

3. case 2:
    : closureList: {*q1,*funcA}, {*q2,*funcA}...

## 4.case

### 1. counter

```go
package main

import (
 "fmt"
)

func counter() func() int {
 a := 0
 return func() int {
  a++
  return a
 }

}
func main() {
 c := counter()
 fmt.Println(c())
 fmt.Println(c())
 fmt.Println(c())
}
```
