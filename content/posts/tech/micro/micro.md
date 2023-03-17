---
title: "微服务"
date: 2021-05-11T22:53:57+08:00
draft: true
tags: ["micro"]
---

[7天用Go从零实现RPC框架GeeRPC](https://geektutu.com/post/geerpc.html)

[一文吃透何为微服务、网关、服务发现/注册？](https://www.wangan.com/p/7fy78y5e5f98b339)

[Microservices vs SOA: What’s the Difference?](https://www.talend.com/resources/microservices-vs-soa/)



##  basic

what's micro:
 一系列自治(autonomous)的服务(进程)共同完成任务:   拆分 and 治理


自治:
1. 独立开发
1. 独立运行: 
	1. 独立完成某项任务
	2. 有自己的db,cache
2. 独立部署: 
	1. 运行在独立容器里 


key characteristic:
1.  高度解耦
1.   去中心化管理
2.  方便更新， 方便开发  


micro vs soa: 
1. 粒度不同:  
	1. soa: 粒度较大，服务于整个企业
	2. micro:  粒度较小，只服务于单个应用
3. 通信方式:
	1. soa: 基于ebs
	2. micro:   restful or grpc

3. 部署方式:
	1. soa: 自建的发布平台
	2. micro:  基于cloud platform 

![4wYxHKbAqz8o](https://raw.githubusercontent.com/atony2099/imgs/master/20230222/4wYxHKbAqz8o.jpg)



micro  vs     distributed system:

same: 微服务是一种特殊的分布式系统
目标不同: 
1. micro: 解耦
2. distrubited: 协作 



micro vs   k8s:
1. micro 是一种架构范式，
2. k8s是实现micro 的平台之一





##  architecture

1. gateway
2.  platform service:
	1.  支撑服务:  与业务服务直接交互
	2.  平台服务:  
		1. 编排服务: k8s
		2. 基础设施

3. bussiness service 
	1.  基础服务
	2.   聚合服务 

![j4VeZj](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/j4VeZj.jpg)


## 服务治理 (service  governance)

what: 
在微服务中，各个服务都是自治的，需要协调和管理好他们;

function based classification :
1. 监测:
	1.  日志
	2.   链路跟踪
2. 通信和路由    
	1. 注册中心
	2. rpc 
	3.  gateway
3. 发布与部署
	1. pipeline
	2.  k8s 
4. 容错
	1. 服务熔断
	2. 服务降级
	3. 限流

implementation hierarchy  clasification:
1. application layr:  与应用直接打交道 
2.  Infrastructure layer: 




![z12xOeW1u62x](https://raw.githubusercontent.com/atony2099/imgs/master/20230226/z12xOeW1u62x.png)





##   fault  tolerence 

what:     服务出现故障时候，仍然可以保持系统正常运作

methods:
1. 限流
2. 熔断
3.  降级(degradation)




##  service divide 

1. 业务模块: 按照经验，与产品充分沟通，确定可能的边切
2. 技术维度:






## basic concept

### 1. service governance

control the service

1. service register and discovery

## cap

consitency: 每个节点都能获取最新信息
availability: 每个节点都能获取响应信息
partition tolerance:
 容忍不同网络分区的通信延迟甚至坏掉
 这是分布式系统最基本要求，必须满足

在网络不稳定的情况下，
要一致性必然导致某段时间内的不可用性
要可用性必然导致数据不一致性

### eventually consistency

## 定义

将单个应用拆分成一系列的松耦合的小服务（arranges an application as a collection of loosely coupled services）

特点：

1. 每个服务独立部署
2. 各个服务可以用不同的语言实现

# go micro

[Architecture](https://www.bookstack.cn/read/micro-en/b0fbd503d5949240.md)

## api

![](https://static.bookstack.cn/projects/micro-en/670b8a436eedffc384141814ec2504f6.png)
provide a single public entry for you services
1.

## implement a micro

1. custom rpc
    1. local balancer
    2. service discovery

    3.

## rpc framework

|                  | Dubbo | Montan                            | rpcx | gRPC               | Thrift         |
| :--------------- | :---- | :-------------------------------- | :--- | :----------------- | :------------- |
| 开发语言         | Java  | Java                              | Go   | 跨语言             | 跨语言         |
| 分布式(服务治理) | √     | √                                 | √    | ×                  | ×              |
| 多序列化框架支持 | √     | √ (当前支持Hessian2、Json,可扩展) | √    | × (只支持protobuf) | × (thrift格式) |
| 多种注册中心     | √     | √                                 | √    | ×                  | ×              |
| 管理中心         | √     | √                                 | √    | ×                  | ×              |
| 跨编程语言       | ×     | × (支持php client和C server)      | ×    | √                  | √              |









