---
title: go architecture
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

[Golang 依赖注入最佳指南 - TeHub - https://tehub.com/a/c0W0jZ5qR8](https://tehub.com/a/c0W0jZ5qR8)


## Solid

核心目标：

1. 最小化修改：
	1. 当需要修改的时候，只修改最小的一部分：单一职责
2. 容易拓展：当需要拓展时候，不改变原有代码，只需要增添新增的部分：  依赖倒置
1.  易阅读，易测试：单一，依赖导致；


在具体实现上：
1. 面相接口编程：将需要的功能尽可能的抽象成接口，并依赖这些接口
2. 接口要分离: 如果实现类并不不需要接口的全部功能，则将接口分离；
3. 划分好类的职责
### single responsibility
单一职责
1.  只负责一类事情
2.  只有当这类 事情发生变化的时候才需要修改代码



example: 负责 user 相关信息的增删改查
```
userclase{

	queryuser
	adduser
	insertuser
	updateUser
	
}

```


how to do： 
1. 选择某个角度去划分职责，同时该项目其他类也尽量按照这个角度
	1. 工作流程： 下单，支付，发货
	2. 功能模块： 订单管理，支付管理
	3. 用户角色：管理员，普通用户
### Dependency Inversion

依赖反转:   
1. 反转高层对底层的依赖， 高层和底层都依赖于一个抽象类
2. 不依赖具体类， 只依赖抽象类; 

依赖注入: 依赖反转 的实现方式；
1. 使用接口间接依赖服务
1. 将依赖传入使用者内部,而不是使用者内部new 出依赖的实例

```
NewUserService(user userDBInterface ){

}
```



open-close:    
1. 对拓展开放，很容易拓展
2. 保持内部的稳定，对于已有的内部代码尽量不动 

里氏替换： 
1. 子类可以完全替代父类
2. 子类必选完全实现父类的抽象方法； 

interface **Segregation**

## opp solid 
是一系列最佳实践总结的规则， 遵守这些规则能写出
容易维护，容易测试，容易阅读的代码 


### single responsibility 

1. 一个class/对象只负责某方面的事情， 只有一个原因会导致代码变化
why: 
1. 

```bash
userRepoClass:
	getUserInof
	saveUserInfo

// 只有 user 表的变化 会导致 userRepoClass变化
```



### Dependency Inversion
依赖反转： 不依赖具体的类，依赖接口

why： 减少其他组件改变对现有组件的影响 


```
type Bussiness  struct {
	repo reponInterface
}

var buss Bussiness{};

buss.repo.dosth;

```

###  open-close

what: 添加新功能不需要修改原有的代码；鼓励使用接口连接各个部件；


###  The Liskov Substitution Principle

what: 子类能够替换任意替换父类，强调子类不能改变父类的行为；  










## inheritance vs composition: 
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



 



##  layer
controller(Presentation)层: 
1. 参数校验 
2. 生成相应

service:
1.  核心的业务逻辑


db: 数据库的操作；



### the basic question

- [ ] 一个文件一个表？




### 如何解决dao层臃肿



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





## high quality  code

1. 易维护；
2.  可能性强
3. 容易测试





