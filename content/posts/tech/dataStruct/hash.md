---
title: hash
date: "2021-09-04T20:28:46+0800"
draft: false
categories: ["datastructure"]
tags: ["hash"]
---


[字典](https://redisbook.readthedocs.io/en/latest/internal-datastruct/dict.html)

[扩容](https://golang.design/go-questions/map/extend/)

[Map实现原理分析](https://studygolang.com/articles/27421)

[深度解密Go语言之 map](https://juejin.im/post/6844903848587296781#heading-8)

[由浅到深，入门Go语言Map实现原理](https://segmentfault.com/a/1190000039101378)
[golang map底层实现](http://yangxikun.github.io/golang/2019/10/07/golang-map.html)

[哈希表](https://draveness.me/golang/docs/part2-foundation/ch03-datastructure/golang-hashmap/#33-%E5%93%88%E5%B8%8C%E8%A1%A8)

[现在面试都这么直接的嘛？(golang map)](https://chowdera.com/2021/04/20210420190818405w.html)


the core to  design a map:
1.   how to get  hash value:     hash funcitonn
2.  how to   handle collision.
3.   how to rehash: grow, 


##  hash function

what:  将输入数据经过一系列计算， 输出固定长度的数据 

types:
1. 加密:  低碰撞，难复原
2. 非加密



why md5 is cryptographic:
1. one-way funciton
2. collision-resistant,



hash function excample

myhash:
```go
func myHash(data []byte) uint32 {
  var hash uint32
  for _, b := range data {
    hash = hash*13 + uint32(b)
  }
  return hash
}
```

djb:  

```go
      func DJBHash(str string) uint {
        var hash uint = 5381
        for i := 0; i < len(str); i++ {
         hash = (hash << 5) + hash + uint(str[i])
        }
        return hash
      }
      
```
 
## handle collision

1. open addressing；开放寻址法；
   继续在原数组上探测一下空位置；
    ![vmeUmV](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210904/vmeUmV.jpg)    

2. separate chaining；分离的链表 
    ![v0P9Oe](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210904/v0P9Oe.jpg)
    
    使用额外的空间存储冲突；
    1. 链表;
    2. 树: 二叉树，红黑树;




## rehash


### grow 

1. what?
   填充度:load_factor= used_buceket/total_bucket
   填充度太大或者太小的时候进行调整；


2. when?
   grow: 填充度 >=1;
   shrink: 填充度 <=0.1

3. how?
   > 创建两组字典；
   1. create  h[1],  h[1].buckets.len = h[2].buckets * 2
   2.  渐进式迁移
       1.  set is hash = true
       2.  rehashIndex++;
          1. rehashstep: 被动
          2. dictRehashMilliseconds:定时任务




### shrink 

