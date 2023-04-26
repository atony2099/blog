---
title: oop in gp 
date: "2021-10-01T13:58:02+0800"
tags: ["oop"]
---
[The SOLID Principles of Object-Oriented Programming Explained in Plain English](https://www.freecodecamp.org/news/solid-principles-explained-in-plain-english/)

[为什么老鸟要告诉你优先使用组合而不是继承？](https://lovoedu.gitee.io/javablog/2017/06/01/20170601/)

[Object Oriented Programming in Go](https://www.ardanlabs.com/blog/2013/07/object-oriented-programming-in-go.html)

[Dependency inversion principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle)

[embedding](https://golang.org/doc/effective_go#embedding)

[Composition over Inheritance](https://medium.com/humans-create-software/composition-over-inheritance-cb6f88070205)

[Trying Clean Architecture on Golang](https://medium.com/hackernoon/golang-clean-archithecture-efd6d7c43047)

[The Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)

[The Clean Architecture — Beginner’s Guide](https://betterprogramming.pub/the-clean-architecture-beginners-guide-e4b7058c1165)

[Object Oriented Inheritance in Go](https://hackthology.com/object-oriented-inheritance-in-go.html "perm link Object Oriented Inheritance in Go")

[Object-Oriented Programming in GO](https://medium.com/@canoguz/object-oriented-programming-in-go-e50f8fe4a620)

[Explaining Clean Architecture](https://oncehub.com/blog/explaining-clean-architecture)

## program   paradigm

program:  a set of instructions

program   paradigm:   

1. 一系列理念和方法, a set of concept,practice  and approaches  to  code
2.  编码的方式 


### types 

1.  命令式, imperative:   如何做
	1. procedural
	2. oop
2. declarative:  做什么,比如 sql, select data from table1,  查询出 data 数据，不管如何具体查
	1.  函数式
	2. reactive
	3. domain-specific language:
		1. sql
		2. html



oop vs procedural
|procedural | oop|

1. procedural
	1.  程序
	2. 

2. oop
	1. 以对象为核心
3. 过程:  指令按照function 分组,
```c
func1:
	do1(data1)
	do2(data2)

func2:
	do3
	do4
```
2.  对象: 指令与它操作的状态(对象)分组


声明， 



### oop vs procedural

1. 



good  code: 易维护代码
1. 隔离变化: 易修改/拓展
3.   少冗余 
4.   高 复用


## oop

what: 基于对象完成任务 

VS： 
1. 面向过程:  function 为基本单元
2. oop:  对象为基本单元; 

object =  data+function 



goal:
1. low couple
2.  easy extend
3. high resuable 


example:
```go
// 1. 减少账户金额
func decreaeAcccount(aocunt int, price int) int{
    return  
}
// 2. 减少库存；
func decreaseProduct(total, decrease int)int{
    return total - decrease
}

var customMoney = 100;
var totalProduct = 10;

customMoney =  decreaeAcccount(customMoney,20)
totalProduct =  decreaseProduce(totalProduct,1)

print(customMoney, totalProduct)
```
```go
class Product(){
    int  TotalCount

    func instructor(count int){
        this.TotalCount = count;
    }
   func decreaseProduct(count int)int{
    return count--
    }
}

class Customer(){
    int  TotalCount

    func instructor(count int){
        this.TotalCount = count;
    }
   func decreaseAccount(count int){
        this.TotalCount -= count
    }
}
// 1. 初始产品数
var p = new Product(10);
// 2. 初始金额
var cus = new Customer(100);

// 1. 减少账户金额
cus.decreaseAccount(20)
// 2. 减少库存
p.decreaeProduct(1)

print(cus.TotalCount, p.TotalCount)

```





###  Encapsulation
![Y7chBP](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211002/Y7chBP.jpg)

what : 
1. 将实体相关数据和行为放在一起；
2. 对外只暴露有限的方法； 对属性的访问通过方法；



benefit: 
1. 隔离变化: 易修改
2. 低耦合: 只暴露必要的 

how: 
1. access level:
	1.  public/private....   methods
	2.  set and getter property
2.  constructor

go: 
1.  no  constructor,  需要自定义
```
NewPerson(name string)
```
2.  less  access  level:   puiblic and private
```
type Person struct {
	  Name string
}

func  Run()
func  cry()
```



###  inherit

是什么:
通过  extend 等关键字获得父类的成员变量和方法；

benefit: resuable 

how: 
1. overwrite
2. subType 
3. call  property and func directly

inherit  in go: 
没有继承， 只有embed(anonymous field)


###  polymorphism
what:  
same  interface have different underlying  data types;
class/function have different  forms

types: 
1.  subType  polymorphism
2.  methods overloading

method overloading: 
```java 
class Calculator{  
	public int sum(int a, int b){  
	return a+b;  
	}  
	  
	public int sum(int a, int b, int c){  
	return a+b+c;  
	}  
}
```

go:   interface  

benefit:
1.  reduce redundancy
2.  easy  extend 



##  oop  design principle:  solid

five: 
1. single-responsibility   principle:  单一职责，每个对象只做一类事情；
2. Dependency Inversion: 依赖接口/抽象类，不依赖具体对象；
    1. normal:  up => Low;
    2. inversion: up-> interfaca <- low
3. The Interface Segregation Principle: 不同接口类型分开；
4. open-close;
5. The Liskov Substitution Principle


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










