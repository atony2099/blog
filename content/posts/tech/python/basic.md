---
title:  basic
date: 2023-12-16T02:05:51+08:00
lastmod: 2023-12-16T02:05:51+08:00
categories: ["python"]
tags: [""]
---



## variables 


python：
1. 动态类型
2. 无需声明；
3. 区分 全局/局部变量：
	1.   function can access global , but can't change it 
	2. 

```
x = 100

def hello():
	y = 200


```



## complex data types


### iterators

```
class MyRange:
    def __init__(self, start, end):
        self.start = start
        self.end = end

    def __iter__(self):
        self.current = self.start
        return self

    def __next__(self):
        if self.current >= self.end:
            raise StopIteration
        else:
            self.current += 1
            return self.current - 1

# Using the custom iterable
for number in MyRange(1, 5):
    print(number)

```

iterable:  product iterator:  reset the start 
iterator:  access element sequentially

iterable  object
1.  list
2. tuple: immutable list      
3. strings
4. set 
5. dict
6. set





basci operation:

1. for loop
```
for x in object


// 1. create a iterator 2. and do next

```
1.  lenght 
2. index and slice 


####  list obsic
1. join two list
2. copy list
3. list ocmper

