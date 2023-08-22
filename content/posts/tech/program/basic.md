---
title:  basic
date: 2023-08-14T20:55:37+08:00
lastmod: 2023-08-14T20:55:37+08:00
categories: ["program"]
tags: [""]
---

## program term 


## data integrity

保持数据的正确性
1.  没有意外的修改 
## slient failure:  

发生故障却没有发出通知，很安静；

result:
1.  diffcult to debug and find the problem
2.   data corruption


##  backpressure 

what: 数据不能正常流通; 
reason: 消费端无法及时消费

危害:
1. 延迟:  生产者阻塞在发送点上，无法继续处理其他工作


如何解决:
1. 控制生产端的速度，（现实很难执行 ）
2. 增加buffer
3. in producer, drop data:
	1. 实时消息: 视频直播
	2.  用户体验: 实时响应
	3. 内存空间有限: 不能提供很多buffer









