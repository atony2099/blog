---
title: module
date: "2021-11-24T07:43:31+0800"
draft: false
categories: ["Go"]
---
[这一次，彻底掌握go mod](https://learnku.com/articles/47737)

[Semantic Import Versioning](https://research.swtch.com/vgo-import)


## go get;

1. exact the path;
2. clone  the path to $GOPATH/src  or $GOPATH/mod

go 基于 git 构建仓库
1. git clone address: package 
2. tag:  package  version


## version name;

v2.3.4: 
major:incompatiable
minor: new feature;
patch version: bug fix

伪版本: 不符合 v1.1.1格式； xxx---xxxxxxxxxxxxx



## go.mod 

1. module name: github地址; 


2. require: 直接引用的包 
    1. incompatiable: major>=2,但 path 不包含 .../vN
    2. indirect: 非直接依赖: 
        current --> B--[direct]-->[C,D];
        C, D  indirect:
        1. B没有module管理，
        2. C,D  没有在B的 go.mod文件中 
   



### how to update major version 


## update

1. go get -u: update current module 






## go.sum

used for: 防止依赖包被篡改 


format:

```
github.com/xxx/pkg1 v1.0.0 h1: hash256value 
github.com/xxx/pkg1 v1.0.0/go.mod  h1: hash256value 
```


what happen before write to go.sum file
1. hash the content and compare hash  value list in sum.golang.org
2. 




what  happen  when  another 



first downl 