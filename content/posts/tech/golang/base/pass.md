---
title: go feature
date: "2021-09-02T20:43:35+0800"
draft: false
categories: ["Go"]
---
[What's the difference between passing by reference vs. passing by value?](https://stackoverflow.com/questions/373419/whats-the-difference-between-passing-by-reference-vs-passing-by-value)

## go中的引用类型；
1. 引用类型；
    1. 存储的是变量的引用(指针);
    2. 不需要显式的取地址；解引用;
    >目标: 共享变量，而不需要显示使用指针;
    ```js 
    let a =  {value:100};
    b = a;
    b.value = 200;
    ```
    
2. 引用类型实现
   c++方式; 
   指针的语法糖；
    ```c
    int i=5;
    int &ri=i;
    ri=8
   ```
    ```js
    int i = 5;
    int const *ri = &i;
    *ri=8
    ```   


3. map, chan, slice 是不是引用类型?
    可以认为是引用类型；
    从结果看: 虽然实现有差异，但是都让我们不用操作指针而共享变量；



## 2. pass by value;

pass by reference: 实参和形参是同一个变量；
pass by value: 实参是实参的拷贝；

大部分语言都是值传递；
> java语言的传递引用对象传递也是值传递；拷贝的是指针;


## 3. oop



