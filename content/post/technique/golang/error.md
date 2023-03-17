---
title: go 的错误处理
date: "2021-02-15T09:20:33+0800"
draft: true
categories: ["error "]
---

1. go 是如何处理error
   1. go处理错误的一般范式
   2. wrap: 包装错误信息 多层传递
      1. wrap 更多错误信息 又不丢失原类型 //
          1. 不丢失源源类型； 更精准去处理错误情况
          2. 一定程度一下保留堆栈信息(?)

2. go 未来展望；

## 错误的处理两种方式

所谓错误就是程序在执行中遇到了与预期不一致的情况，如网络连接错误，文件不存在等。错误处理一般主要有两种方式:

1. 抛出异常(Throw Exception), throw(或者其他类似关键字，如python中的raise)关键字会终止当前程序的执行流程，并不断将错误对象返回上级函数，直到被catch住，如果最终未被catch住则中断程序的运行。

```js
try {
   let area = getSquareArea("unkonwn")
   console.log(area)
} catch (error){
    console.log(error)
}
function getSquareArea(width) {
    if (isNaN(width)) {
      throw new Error("Parameter is not a number!") // 抛出异常
    }
    return width * width
}
```

输出:

```
Error: Parameter is not a number!
    at getSquareArea (/temp/error.js:12:13)
    at Object.<anonymous> (/temp/error.js:3:15)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:74:12)
    at internal/main/run_main_module.js:18:47
```

1. 错误码(值), 使用特殊值标示错误。在c语言中，通常使用-1或NULL表示错误；

```c
int main()
{

    FILE *fp;
    fp = fopen("test.txt", "r");
    if (fp == NULL) // NULL说明未获取文件对象，打卡文件失败
    {
        printf(" Value of errno: %d\n ", errno);
        printf("The error message is : %s\n",
               strerror(errno));
    }
    else
    {
        fclose(fp);
    }
    return 0;
}

```

这两者最大的区别是错误是否作为返回值的一部分，从而导致是否需要立即对错误进行处理或者可以延后处理。
很显然，错误码作为返回值的一部分，开发者能明显感觉到他的存在,这就强制开发者需要对它进行检查处理。

抛出异常的方式错误不是作为返回值一部分，开发者并不能明显感觉它存在。在编码中，一个常见的范式是把多个可能抛出错误的函数放在同一个try...catch体中进行统一处理，然后这样子往往导致不可预期的bug

  ```js
     try{
       let db = CreateDB("db") 
       let table = db.CreateTable("user") 
       let index = table.CreateIndex("user_index")
      }catch(err){
        console.log("unexpected error:",err)
    
      }
 
  ```

   上面CreateDB,CreateTable,CreateIndex这三个函数都可能发生错误，加入在CreateIndex过程中发生错误，那应该是撤销已经创建的db和table,然而在语言层面没有强制需要开发者对每个可能出错的地方检查处理，导致开发者可能对不同错误统一处理，在这个例子中，只是简单打印log, 然后 db 和 table已经创建好了，

 换成c语言处理的情况；虽然比较繁琐，每个函数都要check一下，但却保证少出bug;

 ```c
       mysqlDB *db = CreateDB("db") 
       if (db== null){
          print("create db fail")
          return 
       }
       mysqlIndex *table = db.CreateTable("user") 
       if (table== null){
            print("create table fail")
            deleteDB(db) 
       }
       mysqlIndex *index = table.CreateIndex("user_index")

       if (index== null){
           print("create index fail")
       }
  
 ```

### go处理方式

go采用类似C的错误码，同时由于支持多值返回，一般以返回参数的最后一个值作为错误值；
error 是一个接口类型 只定义了一个错误方法

type Errorf

go的处理更加清晰

```
func ReadFile()(int,error){


}

if error != nil {

}

if error != notfound;{

}

```

go 强制在逻辑里要求开发者即时处理错误

### wrap

在存在多层调用的时候；允许将原错误信息暴露处

## go 的1.13之后的变化

## 展望go 2.0

## go的方式

go 使用错误码，由于多返回值，增强版本；

### 抛出异常

1. 可以简化错误的处理。一段代码可能包含很多异常，可以放在同一个try...catch语句中统一处理，try控制块内只关注正常流程的逻辑,catch对抛出的错误统一处理,可以简化我们

   ```js
   try{
     createDB()
     CreateTable("User")
     CreateIndex("idx_user")
   }catch(err){
     console.log("unexpected error:",err)
   }
   
    
   ```

这个是一个双刃剑；不能显示的检查；常常会导致以外的bug；

## 2. Go的处理方式,增强版本的

## 2. go的处理方式； 错误码

## 3. error的传递

## 3.GO 2的错误处理

## 4. 目前前go处理错误的fangfang's

## 如何简化

## panic

// 1. 确实一个bug

// 2. 程序无法继续下去

// 3.

### 1

1. must do

2.

[](https://devblogs.microsoft.com/oldnewthing/20040422-00/?p=39683)

// panic: can't recover; can't continue

## error check

1. 简化

## 堆栈信息

## error  未来展望

## panic

### 使用场景；确实不可恢复的场景；容易
