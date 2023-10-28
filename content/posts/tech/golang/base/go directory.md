---
title: go directory
date: 2023-10-28T10:51:02+08:00
lastmod: 2023-10-28T10:51:02+08:00
categories:
  - base
tags:
  - go
---
[project-layout/README_zh.md at master · golang-standards/project-layout - https://github.com/golang-standards/project-layout/blob/master/README_zh.md](https://github.com/golang-standards/project-layout/blob/master/README_zh.md)

[paper-code/package-oriented-design/packageorienteddesign.md at master · danceyoung/paper-code - https://github.com/danceyoung/paper-code/blob/master/package-oriented-design/packageorienteddesign.md](https://github.com/danceyoung/paper-code/blob/master/package-oriented-design/packageorienteddesign.md)

## go dir

/cmd: 基本的东西

/internal: 你项目的主要逻辑, 不能被其他项目复用

/pkg:  可以被其他项目复用的



## clean    architecture

![S6j8NF](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211004/S6j8NF.jpg)

![[Pasted image 20230104221801.png]]


principle:  seperate of concern, 分离关注， 每一层只关注自己职责

benefit: 
1. 隔离变化
2. 易复用 



how: 
1. 划分层次
2. 依赖倒置 


layer:
1.  interface  adaptor:  controller, view, presenters ....
	1. get input,  convert  to some format for   use case  
	2. output in a specific format  
1.  use case:   bussiness logic   
2.   entities:  common methods for app，[ and   call repository] ?
4.  repository,  i/o  
	1.  call db by  sql
	2.  call rpc 
	3. call http 


争议:
entities  and repository  如何划分
1. 只保留repository:    repository do io,  common  func 由 util 负责
2. 只保留 entity, entity  do   i/o  and  do common func 
3. 保留 repository  and entity,   entity调用   repository  



