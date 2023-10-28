---
title: go directory
date: 2023-10-28T10:51:02+08:00
lastmod: 2023-10-28T10:51:02+08:00
categories:
  - base
tags:
  - go
---
[project-layout/README_zh.md at master · golang-standards/project-layout](https://github.com/golang-standards/project-layout/blob/master/README_zh.md)

[paper-code/package-oriented-design/packageorienteddesign.md](https://github.com/danceyoung/paper-code/blob/master/package-oriented-design/packageorienteddesign.md)

[How to Keep Your Code Clean With Object Encapsulation](https://www.makeuseof.com/how-to-keep-your-code-clean-with-object-encapsulation/)



## opp solid 
是一系列最佳实践总结的规则， 遵守这些规则能写出
容易维护，容易测试，容易阅读的代码 

### single reason 

1. 一个class/对象只负责某方面的事情， 只有一个原因会导致代码变化

```bash
userRepoClass:
	getUserInof
	saveUserInfo

// 只有 user 表的变化 会导致 userRepoClass变化
```


### Dependency Inversion
依赖导致
1. 对象之前



1. Dependency Inversion: 依赖接口/抽象类，不依赖具体对象；
    1. normal:  up => Low;
    2. inversion: up-> interfaca <- low
2. The Interface Segregation Principle: 不同接口类型分开；
3. open-close;
4. The Liskov Substitution Principle


inheritance vs composition: 
1. inherit: a is b;  a is bird
2. composition: a has {b1,b2..| b is interface or  someType }; a have bird feature(fly,shout) 
   ```java 
   public abstract class Car {
    // 也可以将该方法设置成抽象方法， 强迫子类来实现该方法
    public void brake() {
      // 提供一个默认的刹车实现
      ...
    }
   }
   public class CarModelA extends Car {
    public void brake() {
      aStyleBrake();// A 风格的刹车行为
    }
   }

   public class CarModelB extends Car {

    public void brake() {
      bStyleBrake(); // B 风格的刹车行为
    }
   }
   ```

```java
   public interface IBrakeBehavior {
       public void brake();
   }

   public class AStyleBrake implements IBrakeBehavior {
       public void brake() {
           aStyleBrake(); // A 风格的刹车行为
       }
   }
 

// 同理， 汽车其他的行为（如启动 launch） 也可以用类似的方法实现
// 不同型号的汽车实现， 可以通过赋予不同风格的行为实例来 “组装” 出来的， 也就不需要为 Car 定义不同的子类了 
public class Car{
    protected IBrakeBehavior brakeBehavior;

    public void brake() {
        brakeBehavior.brake();
    }

    public void setBrakeBehavior(final IBrakeBehavior brakeType) {
        this.brakeBehavior = brakeType;
    }
}
```


why inherit is bad:  get all from parent, 与父类过度耦合
1. 继承得过少: 父类无法满足子类所有需求；
2. 继承过多:  父类方法属性对子类不需要；   我们对于需求无法预知；

composition: 按需获取，需要什么属性方法就再加入；











## go structure


/cmd: 基本的东西

/internal: 你项目的主要逻辑, 不能被其他项目复用

/pkg:  可以被其他项目复用的


业务层划分：
1. 通用分层
```bash 

├── internal/
│   └── eventserver/
│       └── biz/
│           └── event/
				└── event.go
│           └── member/
				└── member.go
│       └── data/
│           └── event/
				└── event.go
	
```


1. 功能职责
```bash 

├── internal/
	└── member
	    ├── data.go
	    └── member.go


```



## 通用的问题


### 同一层的包可以互相引用吗？
不能
1. 循环依赖
2. 



 


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



