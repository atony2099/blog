---
title: dataType
date: "2021-11-05T21:54:22+0800"
draft: false
tags: ["dataType"]
lastmod: 2023-03-22T23:41:52+0800
---

[理解C++中引用的底层实现s](https://blog.csdn.net/Mind_V/article/details/78619163)
[# Type Safety in Programming Languages](https://www.baeldung.com/cs/type-safety-programming#:~:text=A%20type%20safe%20language%20maintains,out%2Dof%2Dmemory%20exception.)
[# Statically v. dynamically v. strongly v. weakly typed languages](https://www.educative.io/answers/statically-v-dynamically-v-strongly-v-weakly-typed-languages)

[# Elixir in the Type System Quadrant](https://thinkingelixir.com/elixir-in-the-type-system-quadrant/)

## what

b = 100

1. value  Type: store Direct value;
    a = b;
    a ddress-> [100]

2. reference Type: store Indirect value: address, offset...;
    a = b;
    a address-> [b address]

question:

1. reference type vs pointer type;
    reference type have many forms;reference type = {pointer Type, reference type}
    1. pointer type(): unconst pointr

    ```go
    var q = &a;
    ```

    2. reference type(hidden pointer):

    ```js
    let b = {a:1}
    ```

2. pointer address and dereference;
   1. address(取址): &a, get variable address;
   2. derefernece: visit variable address *a;

### c++ reference type  

1. code

    ```c
    int i=5;
    int &ri=i;
    ri=8
   ```

    ```c
    int i = 5;
    int const *ri = &i;
    *ri=8
    ```

2. reference vs  pointer
    1. reference is const pointer;
    2. reference Auto  addressed and dereferencing;

## how 



## type safety



type check:    
1. the variables  match  the expected type,  if not  throw error or ingore 
2. using the right ingredient when cooking 

static/ dymamic :  compile time/static time check type 

dymanic type
1.  can change type 
2.   don't need  declare the type 

![e7PXe6alI1tk](https://raw.githubusercontent.com/atony2099/imgs/master/20230325/e7PXe6alI1tk.jpg)
![DC6oj2Df1sWy](https://raw.githubusercontent.com/atony2099/imgs/master/20230324/DC6oj2Df1sWy.jpg)

weak/strong type:  多大程度实施它的type system
weak:   无视 type system, 通过implicate conversion  solve type error
strong: 严格遵循type system,  throw type error


type error exmaple:
```python
a = "k"
a/2 

TypeError: unsupported operand type(s) for /: 'str' and 'int
```

why  type  safety is important: avoid unexpected  result (bug)







1.  static  type:   dataType is determine at compile time 
	1.  显示指定类型
2. dymanic type: dataType is determine at run time, can change; 
	1. 不需要显示指定类型:  `c =  100; or let q ="123"`

```python
a = 100
print(a)
a = "qq"
```


why c is weak type?





![xAXncw4LU5bX](https://raw.githubusercontent.com/atony2099/imgs/master/20230323/xAXncw4LU5bX.png)



type safety  data foramt:

在通信的不同进程中，数据的类型是一致的，与schema定义类型一样

