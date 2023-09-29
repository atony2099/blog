---
title:  float
date: 2023-09-28T16:14:21+08:00
lastmod: 2023-09-28T16:14:21+08:00
categories: ["program"]
tags: [""]
---

[IEEE-754浮点数那些坑：0.1加0.2不等于0.3！ | 随笔 - https://fasionchan.com/posts/ieee754-traps/](https://fasionchan.com/posts/ieee754-traps/)

## float 精度问题

### why
二进制无法表示准确的小数？


二进制表示整数是  2^(+n):
101: 2^2+0+2^0 

表示小数则是 2^-n
0.11: 2^-1 + 2^-2=0.5+0.25;  


二进制表示小数就注定无法精确表述，只能无限接近



### cause  problem

1. 无法比较

```
0.1+0.2>0.3

```

1. 解析成json数字很长
```json
{
   "price":0.1000000022
}

```


