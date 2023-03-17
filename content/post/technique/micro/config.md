---
title: "Centralized configuration"
date: 2021-06-01T22:53:57+08:00
tags: ["micro","configuration"]
---

[Nacos 架构](https://nacos.io/zh-cn/docs/architecture.html)


what: 轻量级的数据库，存储非业务相关数据，基于数据库,git实现

store data example:
- db url, password 
-  jwt token


为什么需要:
1. 实时更新
2.  统一管理:  特别是在微服务场景
2.  高级功能 

core function:
1. 实时更新: monitor
2. 多环境配置
3. 权限控制 
4. 版本管理:
	1. version history
	2. 回滚
	3. 灰度发布

5. web管理后台 

the types:
1. custom
1. nacos
2. apolo

custom:
```c
// 1. put config  in db  or github

// 2. 轮训
every n minutes:
	select  data from db;


```



##  list

### spring cloud config

![y58k8q](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/y58k8q.jpg)



### Disconf

![](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/Pasted%20image%2020221228173513.png)



### apollo

![yMk83U](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/yMk83U.jpg)


![y3ECMb](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/y3ECMb.jpg)


## nacos
space:
1. namespace: a  set of group 
2. group: a group of config
3. dataid: a detail config


pros:
1. easy
cons:
1. 更新粒度太大，无法做到增量更新
2. 权限控制太粗糙










## polling 

poll: 反复的检查是否有数据/准备就绪 



http poll:
1. short poll: 服务端立即返回结果
2. long poll:  服务端阻塞客户端直到有结果才返回

![RTeS2H](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/RTeS2H.jpg)



http long poll code:
```c
clientPolling(){

	for{
		data,err = get(api/data)
		if data != nil {
			show(data)
		}
	
	}
}

server.handleRequst(/data, handle)

handle(){

	for {
		data = getdata
		if data == null, sleep(second), continue;
		else: repsone(data)
			
	}

}

```