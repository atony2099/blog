

---
title: error
date: 2023-01-05
description:
tags:["error"]
draft: false
---


error type:
1. exception 
2.  return param
	1. code: -1 , 
	2. result:   null 
	3.  error type




exception:
prons:
1. 可以打印出堆栈
2. 可以统一处

cons: 
错误被统一处理，可能导致潜在bug 


go:  error type
```


type error interface{
	Error() string
}


result, err = doSth(...)
if err != nil: 
```

	

return parameter:
prons:
1. 一对一处理错误，避免bug 
cons:
1. 繁琐， 每个方法都要check一下，  无法统一处理


example:  
creatIndex 失败, 但没有回退之前操作
  ```js
     try{
       let db = CreateDB("db") 
       let table = db.CreateTable("user") 
       let index = table.CreateIndex("user_index")
      }catch(err){
        console.log("unexpected error:",err)
    
      }
 
  ```

强制开发一对一处理错误
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




## wrap
what:
add extra into to a error 

```go
var notFound = errors.New("not found")
err := fmt.Errorf("%w, so bad ", not)


if errors.Is(err,not found){
	.....
}
```





