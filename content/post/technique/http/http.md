---
title: http protocol
date: "2020-12-06T15:44:07+0800"
draft: false
tags: ["http","tcp/ip"]
---

[HTTP/2 头部压缩技术介绍](https://imququ.com/post/header-compression-in-http2.html)

[rfc7540](https://httpwg.org/specs/rfc7540.html)

[Head-of-Line Blocking in QUIC and HTTP/3: The Details](https://calendar.perfplanet.com/2020/head-of-line-blocking-in-quic-and-http-3-the-details)

[Introduction to HTTP/2 ](https://developers.google.com/web/fundamentals/performance/http2)

[HTTP/2 头部压缩技术介绍](https://imququ.com/post/header-compression-in-http2.html)

[HTTP/2.0 Header Compression（上）](https://laoqingcai.com/http2-headercompression/)

[ 队头阻塞（Head-of-Line blocking）](https://www.yazidchen.com/2022/04/19/head-of-line-blocking)

## http structure

1. start line: GET /background.png HTTP/1.1
2. Header:
   1. provide extra message
   2. tell the brower do sth(cache)
3. body: json, xml,etc.

![jiIjXr](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211208/jiIjXr.jpg)


## content-type

|                       | **content-type**                                  |
| :-------------------- | :------------------------------------------------ |
| x-www-form-urlencoded | `application/x-www-form-urlencoded`               |
| form-data             | `multipart/form-data; boundary={boundary string}` |



### cache-control

### load local cache directly

> cache-control, expire
before the time, it will load local;

cache-control:

1. public: allow proxy to cache the data(default value)
2. private: only local browser can cache the data
3. no-cache:   it must go through valiation with origin server;
4. no-store: can't cache
5. max-age=xxx;

### validate the resource from server

> If-Modified-Since: compare resource time
> If-None-Match: compare resource ta ;

if same, return 304; otherwise 200




##  http2
what: 
1. multiplex:  add http2   frame heade
2.  header compresee:small  size header 

![sXzKUS](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/sXzKUS.jpg)




### multiplex

Head-of-Line Blocking: 队列前部阻塞后部

why: http 基于content-length来区分消息的边界，  这要求数据必须是串行的

solve:  http2 frame，
1. 基于frame id   and flag分割消息 
2.  不同资源被交叉发送


http2 frame header:
![f3eZ2R](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/f3eZ2R.jpg)
frame filed:
1. Stream Identifier
2. type:
   1. data:  0x0
   2. headers 0x1
3. flags:
	1.  END_STREAM
	2. END_HEADERS



example:
a.js 2byte
b.js 2byte
header  frame1, data  frame1
header frame1, data frame2 
data frame1, data frame2 

![[Pasted image 20221220164804.png]]

mutiplex strategy:  1111， 2222
1. fair：12121212
2. 加权(2权重是1两倍): 22122122111

### heder compression
![LcvyIl](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211022/LcvyIl.jpg)


使用index代替字段 header.key, header 

```c
:method GET = 2 = 00000010 
:method POST = 3 = 00000011
:path / = 4 = 00000100 
```

huffman coding:

```c
A:0 
B:10 
C:110 
D:111

```


## http keep alive

what: http 未关闭 tcp connection 而 让 tcp connection alive 

http keep alive 基于 tcp-keep-alive 实现的吗:
http 未关闭 tcp connection,此时 tcp 短时间 就是alive, 长时间的alive 才需要 tcp keep alive


##  restful api

what:  
REpresentational **S**tate **T**ransfer， 表现层状态转移
是一种架构风格，  遵循一定的指导原则与约束


restful vs http:
restful 不一定 基于http 实现，
基于 http实现 叫做： HTTP-based RESTful API



methods:
  1. Create — POST
  2. Read/Retrieve — GET
  3. Update — PUT/PATCH
  4. Delete — DELETE

example
```c
   // get all prodducets
   Method: GET
   Endpoint: https://fakestoreapi.com/products

   // retrieve product with id 12
   Method: GET
   ENDPOINT: https://fakestoreapi.com/products/12

   // add new produects
   Method: POST
   ENDPOINT: https://fakestoreapi.com/products
   Data:  {} //Data in form of JSON

   //To update a product resource with id 10
   Method: PUT
   ENDPOINT: https://fakestoreapi.com/products/10
   Data:  {} //Data in form of JSON

   //To delete a resource with id 5:
   Method: DELETE
   ENDPOINT: https://fakestoreapi.com/products/5
```




## URL , URI 

URI：  A Uniform Resource Identifier 
URL : A Uniform Resource Locator,   schema+URI


![6pgDaf](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/6pgDaf.jpg)

![OMD8mS](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/OMD8mS.jpg)

![40qRU9](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/40qRU9.png)