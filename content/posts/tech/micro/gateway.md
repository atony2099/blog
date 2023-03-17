---
title: 网关
date: "2020-12-08T16:38:31+0800"
draft: false
tags: ["微服务","ingress "]
---

[百亿流量微服务网关的设计与实现](https://www.infoq.cn/article/eee1xzeic4udpbmr*03t)

[How NGINX Ingress Controller Works](https://docs.nginx.com/nginx-ingress-controller/intro/how-nginx-ingress-controller-works/)

## what is?

1. 路由
2. 中介策略: 
	1. 安全: ssl
	2. 过滤:
		1. 黑名单 
	3. 流控

### 1. 分类

![](https://static001.infoq.cn/resource/image/93/5b/939d3076ec509a3504d5fc40234ded5b.jpg)

1. 流量网:通用的功能，与业务无关
2. 业务网关: 与具体业务相关联

二者也可能合二为一

## 2. vs

![](https://static001.infoq.cn/resource/image/76/31/767492a59796925f9647c4b7055b5d31.jpg)


## zuul  gateway

core:
1. rouite 
2. filter

### router


传统方式: 
```c
```yaml
#Zuul实现的传统的路由配置
zuul:
  routes:
    hello-server-url:
      path: /hello-server/**
      url: http://localhost:9003
```


面向服务:  结合服务注册中心

```c
# Zuul面向服务的配置服务 
zuul: 
	routes: 
		api-hello-server: 
			path: /hello-server/
			service-id: hello-server 
		api-customer-server: 
			path: /customer-server/
			service-id: customer-server

```


## ingress controller 

what: k8s网关，基于 nginx实现

![9LYyEP](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/9LYyEP.jpg)


