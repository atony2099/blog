---
title: Go中转换类型
date: "2020-12-02T18:24:13+0800"
draft: false
category: ["Go"]
---

## type assertion：类型断言
适用于 interface{}; 将 interface{}转换为具体类型；可能会失败

```go
var a interface{} = 100;
value,ok := a.(int)
```


## convertion: 类型转换
only for  numerical type

### 1. widening conversion(implict conversion)

go dont's support 
```
class Test 
{ 
    public static void main(String[] args) 
    { 
        int i = 100;  
          
        // automatic type conversion 
        long l = i;  
          
        // automatic type conversion 
        float f = l;  
        System.out.println("Int value "+i); 
        System.out.println("Long value "+l); 
        System.out.println("Float value "+f); 
    } 
} 
```



### 2. narrowing conversion(explicit conversion)
```
class Test 
{ 
    public static void main(String[] args) 
    { 
        double d = 100.04;  
          
        //explicit type casting 
        long l = (long)d; 
          
        //explicit type casting  
        int i = (int)l;  
        System.out.println("Double value "+d); 
          
        //fractional part lost 
        System.out.println("Long value "+l);  
          
        //fractional part lost 
        System.out.println("Int value "+i);  
    }  
} 
```


### for go
in go, it dosen't matter if  the type widen or narrow, its conversion must be explict;s


```go
var a int = b;
var b float64 = float64(a)
```