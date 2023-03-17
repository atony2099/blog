
---
title: queue
date: 2021-11-21
description:
tags:["queue","datastructure"]
draft: false
---


##  basic operation 

op:
1. enqueue
2. dequeue 


```c
#define  size  5 
int item[size]


```



## circular  queue

![[Pasted image 20221121175921.png]]


op:
```c
var queueSize = 6
var header,tail  = -1
var circularQ [queueSize]int


isEmpty:
	front= -1

dequeue:
	if isEmpty, return;


	ele =  circularQ[front]

	front = front++ mod  queueSize;

	return ele 

```



also called: ring buffer



dequeue  








## the used   case 

