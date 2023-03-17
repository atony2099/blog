---
title: "其他"
date: 2020-03-30T21:26:51+08:00
draft: true
categories: ["其他"]
---



## pseudorandom number 伪随机数



### algorithm

```
Xn+1 = (aXn + c) mod m
where X is the sequence of pseudo-random values
m, 0 < m  - modulus 
a, 0 < a < m  - multiplier
c, 0 ≤ c < m  - increment
x0, 0 ≤ x0 < m  - the seed or start value
```





### feature 

1. not truely random, **deterministic**

   本质上是根据同一套算法和初始值生成不同 数字序列； 如果初始值不变，那每次生成序列是固定的

