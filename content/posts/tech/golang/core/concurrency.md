---
title:  concurrency
date: 2023-08-31T01:08:56+08:00
lastmod: 2023-08-31T01:08:56+08:00
categories: ["core"]
tags: [""]
---


types:
1.  share,  并行的；
1.  no share,   线性的

线性:
1. csp modal

csp modal: 
1. 使用channel 共享数据


the key pointer in  csp program:  
1. avoid consumer  leak
1. close producer
2. consumer 手动计数 

1. close channel
```
go func:
go func:

```

1. avoid  channel leak   
```


```

how to  use csp program:


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


