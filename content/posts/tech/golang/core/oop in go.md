---
title: oop in go 
date: "2021-10-01T13:58:02+0800"
tags: ["oop"]
lastmod: 2023-04-27T21:03:20+0800
---

[Type embedding: Golang's fake inheritance | DoltHub Blog ](https://www.dolthub.com/blog/2023-02-22-golangs-fake-inheritance/)

[Differences between Procedural and Object Oriented Programming - GeeksforGeeks ](https://www.geeksforgeeks.org/differences-between-procedural-and-object-oriented-programming/)

[How to Keep Your Code Clean With Object Encapsulation](https://www.makeuseof.com/how-to-keep-your-code-clean-with-object-encapsulation/)

[Differences between Procedural and Object Oriented Programming ](https://www.tutorialspoint.com/differences-between-procedural-and-object-oriented-programming)

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



###  oop vs procedural:
| parame               | procedural      | oop         |
| -------------------- | --------------- | ----------- |
| 程序划分为           | function        | object      |
| maintain             | easy            | hard        |
| **code reusability** | no              | inheritance |
| **Priority**         | function        | data        |
| access modifier      | no, less secure | yes         |
| abstrct                     |     无            |     基于现实世界进行抽象        |


procedural
```python
# Procedure to calculate the area of a rectangle
def calculate_area(length, width):
    area = length * width
    return area

# Prompt user for input
length = float(input("Enter the length of the rectangle: "))
width = float(input("Enter the width of the rectangle: "))

# Calculate area using the procedure
area = calculate_area(length, width)

# Display result
print("The area of the rectangle is:", area)

```
oop
```python
# Rectangle class
class Rectangle:
    def __init__(self, length, width):
        self.length = length
        self.width = width
        
    def calculate_area(self):
        area = self.length * self.width
        return area

# Prompt user for input
length = float(input("Enter the length of the rectangle: "))
width = float(input("Enter the width of the rectangle: "))

# Create Rectangle object and calculate area
rect = Rectangle(length, width)
area = rect.calculate_area()

# Display result
print("The area of the rectangle is:", area)

```


good  code: 易维护代码
1. 隔离变化: 易修改/拓展
3.   少冗余 
4.   高 复用





## oop 


### what

oop: 
1. 模拟真实世界，构建出对象， 通过对象之间的交互完成相关逻辑
2. 通过封装继承多态构建出好的代码

the pros:
1. 封装： 模块化 ，隔离变化， 容易维护 
2. 继承：代码复用，减少冗余 
3. 多态: 可拓展性



go isn't prue oop:
1. go缺失了继承 
2. go 没有其他oop语言里常见元素: class, construtctor
3. 封装支持的level只有 public and private


highlight:
1. 抛弃了oop的繁重的inherit and class
2. 使用简单的方式实现oop



### principle

what's principle: 
1. 原则,原理， 指导方针， 实现oop 的基础

four principle in oop  
1. 抽象:
2. 封装
3. 继承
4. 多态



####  Encapsulation
![Y7chBP](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211002/Y7chBP.jpg)

also:   hiding info 

what: 将数据和方法打包到一个对象中，并只开放必要方法访问和操作数据 
```
class  person:
	privated  int age;
	public  AddAge(int year):
		age+= year
```


in go: methods + low,uppercase
```go
type Person struct{
	age int
}
func (p *Person)AddAge(int year){
	p.age+=year
}
```



example:

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return this.name;
    }

    public int getAge() {
        return this.age;
    }

    public void setAge(int age) {
        if (age >= 0 && age <= 120) {
            this.age = age;
        } else {
            throw new IllegalArgumentException("Age must be between 0 and 120.");
        }
    }
}

```

###### how go  do it  

1. 使用type and methods  模仿 class, 
2. 使用大小写控制访问权限: packge level 

```go

type Person struct {
	name  string // private
}

func (p *Person)GetName()string{
	return p.name
}

func (p *Person)SetName(name string)string {
	p.name = name
}



```


####  inherit

what:  直接获得已有对象的属性和方法 

pros: 代码复用，减少冗余 
cons:  父类与子类强耦合，不易于维护


#####  inherit   in go 
go 没有继承，只有组合:
1. 普通组合:  成员变量
2. 内嵌: 匿名成员变量

```go

type Dog  struct{
	Base Animal
}

type Dog struct{
	Animal
}

```

embed: 可以直接调用匿名成员变量的属性和方法 

为什么 embeded不是继承：

1. 没有多态的特性: B is A; var B A;
2. 无法重写方法

```go
package main

import "fmt"

type Person struct {
}

func (p *Person) Greet() {
	var n = p.Name()
	fmt.Println("hello:",n)
}

func (p *Person) Name() string {
	return "default"
}

type Male struct {
	*Person
}

func (m *Male) Name() string {
	return "tony"
}

func main() {

	var f Male

	f.Greet() //  expected "hello:tang "

}

```

是组合的语法糖

```go
f.Greet()== f.Person.Greet()
f.Name == f.Person.Name

```


####  polymorphism

what:  不同形态(object)响应相同的接口

pros:
1. 可拓展性

in java: 继承 and interface  
```
class Animal:
	makesound:


class Dog extends Animal:
	makesound:


class Bird implemnts Aninal:
	makesound

```
##### polymorphism in go 

limit: go只能 使用interface实现多态

highlight:  不需要显式的指定 实现interface 

interface example : 
```go

type Bird interface {
	fly()
}

type Eagle struct {}

func (e *Eagele)fly(){
	....
}

func main() {
	var ma Bird = Mahjong{"mahjong"}
	var a Bird = Eagle{"Eagle"}

	var birdList = []Bird{ma, a}
	for _, v := range birdList {
		v.fly()
	}
}
```



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










