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

what: 
1. 一种机制， 确保变量只能符合其类型的方式进行操作 
```

if   operation  not compatible myType:
	throw error
else
	operation(myTyp)
```

how： detect at compile and runtime, then throw it 

type error：a error   when an operation is performed on a variable that's not compatible with its data type 


why  type  safety is important:
1. 



strong vs weak type:
1. strong type: chekc type error  and throw
2. weak type:  ingore type and  type erorr
	1. auto implict type conversion 

strong type :
1.  static  type:   dataType is determine at compile time 
	1.  显示指定类型
2. dymanic type: dataType is determine at run time 
	1. 不需要显示指定类型:  `c =  100; or let q ="123"`
	2.  可以更换类型： 
		
```python
a = 100
print(a)
a = "qq"
```




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




