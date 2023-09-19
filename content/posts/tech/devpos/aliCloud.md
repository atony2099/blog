---
title: nat
date: "2021-03-01T18:13:39+0800"
draft: false
categories: ["nat"]
---


[专有网络VPC的视频简介和组成_专有网络 VPC-阿里云帮助中心 - https://help.aliyun.com/zh/vpc/product-overview/what-is-a-vpc](https://help.aliyun.com/zh/vpc/product-overview/what-is-a-vpc)
## switch vs  router

1. switch
    store mac address in a lookup table
2. router
   store ip address in the routing table;

   forward data packet between computer networks

## NAT

network address transalation

snat:  translate source ip
dnat: translate destination ip ;

[详解SLB、EIP、NAT网关之间区别， 合理选择云上公网入口](https://developer.aliyun.com/article/391631)

## public ip

1. eip (elastic ip):弹性ip
2. nat (network address translation): nat网关
3. slb（server load balance)

底层都是都过nat实现






## 可用区
可用区：同一地区的不同的地方

同一地域的不同可用区内网互联
![xMv0ewUcE9Ol](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230919/xMv0ewUcE9Ol.png)



![o0mG38yqYNLw](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230919/o0mG38yqYNLw.png)