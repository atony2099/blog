---
title: 原型链
date: "2020-12-26T22:00:13+0800"
draft: false
categories: ["js"]
---


# 原型与继承

## 是什么？

![](https://www.w3cmm.com/wp-content/uploads/2013/03/prototype-chain-02.png)



1. 每个构造函数内部都有一个prototype属性;指向一个原型对象,每个对象实例内部都有一个prototype属性，指向同一个原型对象。

```js
function Person() {}
let person1 = new Person();
person1.__proto__ == Person.prototype;
```

通过改变构造函数指向的原型对象，来构造继承关系

```js
 function SuperType(){
   this.Propertype = true;
 }
 SuperType.prototype.getSuperValue = function(){
  return this.Propertype;
 }

function SubType()
  this.Propertype = false;
}

SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function(){
  return this.Propertype;
}

let sub = new SubType()
console.log(sub.getSuperValue());
```



