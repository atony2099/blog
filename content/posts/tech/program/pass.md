---
title:  psss by value 
date: 2023-04-30T23:53:20+08:00
lastmod: 2023-04-30T23:53:20+08:00
categories: ["other"]
tags: [""]
---



pass by value:  pass the copy value of  the arguments 
feture: any change made to the parameter won't affect the origin value

pass by reference: pass the reference  of the arguments
feature: any change made to the  parameter will affect the orgin value


example:
```python
def increse(number): # number= the copy of x
	number++

x=100
increae(x)

```

```c++

// num = the reference(pointer) of x
void increment(int& num) {
    num++;
}

int x = 5;
increment(x);
cout << x; // output: 6

```



problem: 为什么指针作为参数可以改变指针指向的值，却不是传递引用

1. 参数传递的还是指针的拷贝值
2. 对指针本身值的改变并不会影响外部指针的值
3. 混淆点: 并不是对指针本身的值改变，而是对指针指向值的改变

