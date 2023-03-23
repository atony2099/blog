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

what: 
一种机制, 在一个程序中，  确保变量只能符合其类型的方式进行操作
throw type error  when  operation is not compatible with the type  
```

if   operation  not compatible myType:
	throw  type error 
else
	operation(myTyp)
```


how： detect at compile and runtime, then throw it 


type error exmaple:
```python
a = "k"
a/2 

TypeError: unsupported operand type(s) for /: 'str' and 'int
```

why  type  safety is important: avoid unexpected  result (bug)


strong vs weak type:
1.   

1. strong type: type-safety,   check  type error  and throw error 
2. weak type:  ingore type and  type erorr
	1. auto implict type conversion 


static vs dymanic 

1.  static  type:   dataType is determine at compile time 
	1.  显示指定类型
2. dymanic type: dataType is determine at run time, can change; 
	1. 不需要显示指定类型:  `c =  100; or let q ="123"`

```python
a = 100
print(a)
a = "qq"
```



![xAXncw4LU5bX](https://raw.githubusercontent.com/atony2099/imgs/master/20230323/xAXncw4LU5bX.png)



type safety  data foramt:

在通信的不同进程中，数据的类型是一致的，与schema定义类型一样

