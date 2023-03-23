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


一种机制， 保证

what：  a language can detect  type error  at compile time or run time,  then report(throw ) it 

type error：a error   when an operation is performed on a variable that's not compatible with its data type 

why type error is important:
1.  cause unexpect behavior 
2.  

strong vs weak type:
1. strong type:   强调类型，对
	1. 不符合类型的操作时候，会报错
	2. 不同类型转换需要显示转换 

2. weak type:
	1. what: 允许 type-error, 

strong type:
1.  static  type:   detect type error at compile time
2. dymanic type:  detect type error at  run 




```python
a = "12"
a/2

TypeError: unsupported operand type(s) for /: 'str' and 'int
```

1. weak type: 类型不重要，不符合类型的操作时候，会自动转换类型
```js
let a ="123"
a/2

```


static vs   dymanic-checking type :

1. static:  编译期 类型已经确定， 需要显示指定其类型, jave
2. dymanic: 运行期 类型再确定，
	1. 不需要显示指定类型，
	2. 可以更换类型 


how:
validate the variabl , and throw error 




is python tyope 



way:
1. comp


## strong  type vs weak type

strong type:  a variable must have a type,  and only that types
weak type:




