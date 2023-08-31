
---

title: gozero
date: 2021-12-26
description:
tags: ["gozero"]
draft: false
---

microservice framework:
提供工具以方便构建微服务，通常以rpc 通信为核心  



framework basic tool:

1. service discovery:  based on  etcd,consual
2. transport : based on grpc ....

go zero 框架:


1. go-kit
2. go-micro
3. go-zero

go-micro:

gateway-> aggregated service->  single service
![6ktJKGdKKmy4](https://raw.githubusercontent.com/atony2099/imgs/master/20230301/6ktJKGdKKmy4.jpg)

## go zero

architecture:  基于rpc封装；

1. api 聚合服务
2. rpc  单个服务

the component:
api:  聚合服务
rpc：特定服务

服务注册发现

basic
router: path, handler
hander:  srvContext,

## arhitecture

框架特点:

1. controller
2. service
 1. service.repo
 2. service.rpc

rpc 框架

1. api
 1. handler-> logic: call rpc
 3. rpc
  1. servicesA
  2. servicesB

rest  server:

```c
restServer =  newServer()
ctx  = newContext(rpcClient) 

register(restServer, handler):
 restServer.addRoute(path, handler(ctx))

```

rpc server:

```c
rpcServer  = newZrpcServer()

ctx = nnewContext(db, rpc,....)
serverImp = newServerImp(ctx)

protocol.register(rpcServer, serverImp)

```

go zero框架 是什么:

1. rpc封装:  context;
 2. client.SvxContextg

基于rpc 的封装， baohang
