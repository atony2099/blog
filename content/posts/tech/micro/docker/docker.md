---
title: docker
date: "2021-01-27T15:18:46+0800"
draft: false
tags: ["k8s","docker"]
---

[What’s the Diff: VMs vs Containers](https://www.backblaze.com/blog/vm-vs-containers/)

[Virtualization](https://www.backblaze.com/blog/vm-vs-containers/)

[虚拟化技术概念学习总结](https://cloud.tencent.com/developer/article/1782543)

[Docker 核心技术与实现原理](https://draveness.me/docker/)

[深入分析 Docker 镜像原理](http://blog.daocloud.io/principle-of-docker-image/)

[深入分析 Docker 镜像原理](http://blog.daocloud.io/principle-of-docker-image/)

## virtualization

what:  将硬件资源重新分配， 将一套硬件资源虚拟成多套硬件资源 ,   从而运行多个不同的操作系统

涉及的技术:
1. resource managent:  分配硬件资源
2. isolation: 隔离多个操作系统 
3. .....


types:
1. 硬件虚拟化(hardware): hypervisor完成虚拟化
2. 操作系统虚拟化(OS-level virtualization ): os 完成虚拟化 

images:
![qOmNin](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211204/qOmNin.jpg)


differ:
1. 虚拟的软件:
	1. hypervior
	2. os

2. isolation level:
	1.  os level
	2. process  level 

 docker: 基于linux实现
1.  namespace隔离 进程，文件系统，网络
2.  CGroups 隔离硬件资源(cpu,ram)


## docker  

docker package your application and all its dependencyies((including os)) together in  a form of container so as to ensure that your application work seamlessly in any envirorment;

###  architecture

![9eOKB6](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210713/9eOKB6.jpg)

![2QQD6g](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211204/2QQD6g.jpg)

client-service;

1. client;
2. service:daemon

client->daemon: pull image from registry, run  image


###  container,  images

![tyJSB1](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/tyJSB1.jpg)


image:  运行程序所需要的 源文件， os 环境 

container: 运行在一个独立os环境的进程 
1.   a instance of a image


image  example: 

```yml 
FROM golang:1.16-alpine

WORKDIR /app

COPY go.mod ./
COPY go.sum ./
RUN go mod download

COPY *.go ./

RUN go build -o /docker-gs-ping

EXPOSE 8080

CMD [ "/docker-gs-ping" ]
```






##  docker compose 

what:  
1. 将docker run  的参数写入配置文件
2. 同时执行 多个 


##  docker volume

what: 数据持久化 


types:
1.  docker volume: 命名 and  匿名；
	1. docker 创建和管理， docker 有完全的权限
	2. 可以通过 docker 命令管理   
	3. 适合非配置的数据 


1.  bind  mount：
	1. 一般是用户创建的文件， docker 不一定有完全的权限 
	2. 不可以通过docker命令管理
	3. 适合配置类型的数据 

2. what? persiste data;
3. how 
   ```c
   docker run -d \
   --name=nginxtest \
   -v nginx-vol:/usr/share/nginx/html \
   nginx:latest
  ```


 








## docker command

image name syntax:
> [reigistry host name ]imageName:[tag]
for: aliyun.jenkins/centos:verson1.0;

1. build  image
    docker build  -t name:[tag] .
2. run contianer
    docker run  
    1. -d: run container in background
    2. --rm:Automatically remove the container when it exits
    3. -p: publish a container's port to the host
3. docker tag  sourceimage[tag] targetimage[tag]
    create a new image for souce image;
4. docker rmi
   remove images

## dockerfile instrution

1. COPY: COPY SRC DEST
   copy files from src and add them to the filesystem of the container  at dest
2. WORKDIR
    set the working directory fro any RUN,CMD,COPY and ADD instruction


## docker-compose







