---
title:  concurrency
date: 2023-08-31T01:08:56+08:00
lastmod: 2023-08-31T01:08:56+08:00
categories: ["core"]
tags: [""]
---

concurrency types:
1.  share:  并行处理数据， 使用locker 共享数据
1.  no share:  线性的处理数据，使用channel  共享数据 



the key pointer in  csp program:  
1. avoid consumer  leak
	1. close producer
	2. consumer 手动计数 
```go

// close channel.
channel
wait+=2
go func:
 defer wait.done
 channel<-1
	
go func:
  defer wait.done
  channel<-1
go func()
	wait.wait
	close(channel )

for range channe:
	


// 手动计数
for  i<=2:
	<-channel


```
1. avoid producer leak: set channel buffer
```go
channel = make(chan int,  producerCount)
for i<=producerCount:
	go func:  channel<-datxa

```


how to   use csp  in concurrency program:
1. 数据线性流通，从producer 流向consumr
2. producer 可以并发获取数据，consumer处理数据，一般一个goroutine，不并发


1.  mutiple producer and one consumer
```
chan 
go func:
	...
	chan<-data
	
go func:
	chan<-data


consumer func:
	for range  chan:
		 data += <-chan




```

1.  one producer->mutiple consumer->
```

```
