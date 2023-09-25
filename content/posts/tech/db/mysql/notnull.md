---
title: not null
date: "2021-01-23T19:22:08+0800"
draft: false
tags: ["mysql"]
---

[MySQL中IS NULL、IS NOT NULL、!=不能用索引？胡扯！ - 掘金 - https://juejin.cn/post/6844903921450745863](https://juejin.cn/post/6844903921450745863)

[InnoDB索引允许NULL对性能有影响吗-腾讯云开发者社区-腾讯云 - https://cloud.tencent.com/developer/article/1658064](https://cloud.tencent.com/developer/article/1658064)

[InnoDB索引允许NULL对性能有影响吗-腾讯云开发者社区-腾讯云 - https://cloud.tencent.com/developer/article/1658064](https://cloud.tencent.com/developer/article/1658064)

the cons and pros of using null

the cons:
性能开销
1. 增加存储空间,需要使用 额外一个bit 记录是否为null，但是使用非null默认值如0, "",占用空间反而更大
2. 索引失效: 并不会使得索引失效

使用开销: 不方便查询，特别是聚合查询，没有加 is null,not null 可能会导致查询结果失准

```
// wrong
select coutn(name) from person

// right 
select count(*) from person 


// wronng
select * from person where name!='Java' order by id;

// correct:
select * from person where name!='Java'or   name is null   order by id;

```


the pros :
在表示一些 确实没有 的数据， 如邮箱， 号码， 直接使用null 会比使用默认值更容易理解；并且不需要创造一个默认值 


结论:

1. null wei会使用所用
2. 如果业务中确实 有意义，比如缺失的号码，缺失的出生日期，那放心使用null；


## what is null?

indicate that the value is missing or unkonwn;

## compare with null

```sql
select NULL IS NULL # true
select NULL IS NOT  null # true
select NULL <=> NULL # true
```

but it will result in a null value when using  =, != <=, >=

## the problem of using null

### 1. select problem

1. null rows is eliminated from count function, and
    negative selection(such as !=, Not like, not in)

2. index problem
    index  need one extra byte to record nullable field
