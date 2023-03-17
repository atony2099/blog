---
title: compile
date: "2021-09-02T13:50:27+0800"
draft: false
categories: ["Go"]
---

[被知乎大佬嘲讽后的一个月，我重新研究了一下内联函数](https://zhuanlan.zhihu.com/p/50812510)

## overview 

![J6UUIC](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210902/J6UUIC.jpg)

1. 生成抽象语法树
2. 类型检查；
3. 根据抽象语法树生成目标代码





## 内联优化
直接展开函数，从而减少函数调用的开销；




## parese tree;


## command;

### -gcflags
1. -m:	print optimization decisions;
2. -l:  disable inline;
