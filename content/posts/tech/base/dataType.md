---
title: dataType
date: "2021-11-05T21:54:22+0800"
draft: false
tags: ["type"]
lastmod: 2023-03-22T23:41:52+0800
---

[理解C++中引用的底层实现s](https://blog.csdn.net/Mind_V/article/details/78619163)
[# Type Safety in Programming Languages](https://www.baeldung.com/cs/type-safety-programming#:~:text=A%20type%20safe%20language%20maintains,out%2Dof%2Dmemory%20exception.)

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

what：prevent type error

type error：对变量操作 不符合变量实际类型, 如对string 进行 数学运算  

how:
1.  static  chekck
2. dymanic chekck


strong vs weak type:

1




static vs   dymanic :


how:
validate the variabl , and throw error 




is python tyope 



way:
1. comp


## strong  type vs weak type

strong type:  a variable must have a type,  and only that types
weak type:




