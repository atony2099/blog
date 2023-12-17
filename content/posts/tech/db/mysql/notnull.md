---
title: null value
date: 2021-01-23T19:22:08+0800
draft: false
tags:
  - mysql
---

[MySQL中IS NULL、IS NOT NULL、!=不能用索引？胡扯！ - 掘金 - https://juejin.cn/post/6844903921450745863](https://juejin.cn/post/6844903921450745863)

[InnoDB索引允许NULL对性能有影响吗-腾讯云开发者社区-腾讯云 - https://cloud.tencent.com/developer/article/1658064](https://cloud.tencent.com/developer/article/1658064)

[InnoDB索引允许NULL对性能有影响吗-腾讯云开发者社区-腾讯云 - https://cloud.tencent.com/developer/article/1658064](https://cloud.tencent.com/developer/article/1658064)


null value：
1. 没有该属性
2. 有该属性，但是暂时不知道

feature: 基于null 的比较和运算结果都是null
```
select null = null // null
select 100 + null// null
```

the cons and pros of using null

the cons:
性能开销
1. 增加存储空间,需要使用 额外一个bit 记录是否为null，但是使用非null默认值如0, "",占用空间有时候反而更大
2. 索引失效: 并不会使得索引失效
3. 使用开销: 不方便查询，特别是聚合查询，没有加 is null,not null 可能会导致查询结果失准

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

1. 使用 null 并不会带来额外性能开销；
2. 如果业务中确实 有意义，比如缺失的号码，缺失的出生日期，那放心使用null；


是否使用null:
直接从业务角度考虑，如果默认值合适，优先使用默认值，否则使用null, 因为null 会增加查询的复杂度; 

example:
1. buy_count: 使用 not null default 0； 默认值可以表示，就使用默认值l; 
2. birth_day: 使用  default null;  此时没有合适的默认值表示


the null  function：
is (not) null ;

ifnull(null, 0)








##  null 是如何存储的;

对于 nullable  字段
使用  1bit 记录是否为null 值 ；

![81rQmxhyoEKw](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20231217/81rQmxhyoEKw.png)

