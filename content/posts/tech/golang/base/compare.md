---
title: go types
date: "2021-02-28T09:17:11+0800"
draft: false
categories: ["Go"]
---

## comparale;

1. what？
   use "==" to compare

2. comparable:
    1. basic type
    2. channel: equal when same reference;
    3. arrays: if elements are comparable;
    4. struct: if  fields are comparable;
    5. interface:   
        if: dymanic types are comparable
        OR: panic,comparable uncomparable types;
3. uncomparable:
   1. map;
   2. function
   3. slice
   
4. how to compare funciton, slice, map;
   `reflect.Deepequal`
   1. slice equal when : all element ==
   2. map: keys,values are  ==;
   3. function: both nil





## defer

### what
1. what？
   a function will be called at last;   
2. feature?
   1. stack: FILO;
   2. 

## how 
1. linked list
    node: {deferF *func,parameters interface{}}

    1. stack structure;
    2. copy parameters immediately
    
2. case
   ```go
   func main(){
       a := 1
       defer  fmt.Println("1",s)
       a++
       defer func(){
           fmt.Println("2",a)
       }
   }
   ```
   output:
   "2", 2;
   "1", 1
   1. defer 基于链表形成栈结构，先进后出
   2. defer参数会理解拷贝到defer结构体中,后面的修改不影响，"1",1
   3. 非defer参数，不会被拷贝，在defer参数执行的时候获取它的最终值；；






### use case;

1. clean action:
   1. close i/o, channel; 
   2. cancel Context


## type conversion;

int <-> float;

```go
var a = 100;
var c = float32(a)+ 1.0
```

## type assertion

interface{} -> specific type

1. get types;
   1.  switch; 
      ```go
         switch x.(type){
            case int:
            case string:
            default:

         }

      ```
   2. xType:=reflect.TypeOf(x);