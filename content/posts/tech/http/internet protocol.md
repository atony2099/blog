---
title: internet protocol
date: "2021-07-20T01:08:22+0800"
draft: false 
tags: ["tcp/ip"]
---

[IP分片](https://fasionchan.com/network/ip/fragmentation/)

[ping 的工作原理](https://xiaolincoding.com/network/4_ip/ping.htm)

##  ip protocol 

![wYvJXK](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210702/wYvJXK.jpg)
![Xm3gsF](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210711/Xm3gsF.png)

1. header size:  20bytes <= ip header size  <= 60byte
2. ip4 range: 0.0.0.0 ~ 255.255.255.255;  2 * 32 ip(4.3billion);
3. why need ip layer?
    if we  connect  all computer, we must group the mac; mac address is hard for group;
    ip is a virtual address which easy to group , so we can build a big network;

## divide the addrees

1. why?
    to reach the destination more efficiently

2. how?
   divie into two parts: net part and local part
   using CIDR(classless inter-domain routing)
   1. subnet masking
   2. CIDR notion
      ip/n
      1~n position :  network part;
      n-32 position:   local part;  

      172.17.100.0/24  
      network: 172.17.100;
      local: 0~255;

      172.17.100.0/22  
      network: 172.17.10
      local: (0~3).255



	

      represent specific ip;
          1. 192.168.100.14/24;  net: 192.168.100, local: 14
3. how ip work
    1. check destionation;
        1. in same network, 192.168.1.0/24, send directly
        2. in different network  192.168.2.0/24. send to gateway(router)

## 3. route table


```shell

##  host: 192.168.1.1  in first network
   Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
   192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 eth0
   192.168.2.0     192.168.1.1     255.255.255.0   UG    0      0        0 eth0

## routr:   192.168.1.1;  192.168.2.1
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 eth1
192.168.2.0     0.0.0.0         255.255.255.0   U     0      0        0 eth2



##  host: 192.168.2.2  in second network
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.1.0     192.168.2.1     255.255.255.0   UG    0      0        0 eth0
192.168.2.0     0.0.0.0         255.255.255.0   U     0      0        0 eth0
```

1. 是什么?
    定义各个ip段如何通信；
    gateway 
    1. gateway 0.0.0.0 : 直接通信；
    2. 其他: 通过router 转发；



## 4. NAT

network address  translation

1. Source nat

personal host for visit internets
192.1
        ---[source:192.1 ]-->[ SNAT ] --->[source:45.1] ---->
192.2  

1. Destination  nat

websizeHost
192.1
        <---[dest: 192.1]--[ NAT ] <--- [dest: 45.1]----
192.2  



## 5. ip fragment

1. when?
    upperData > mtu;

2. fragment:
    origin:    [10000];
    ip datagram: {datagram1...datagramN | datagram1.size=mtu}  
    
3. reassemble:
    ![pPEV3s](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210719/pPEV3s.jpg)
    
    ​	 By id,offset;
    
    ​     Fail:  in N Times,   reassemble Buffer get all datagram, it send icmp error;
    
    ​	
    
    
    
    



##   arp   protocol

1. what?

cacheTable{ip:mac}

ip->mac;


```
192.168.124.49 (192.168.124.49) at 1a:db:37:87:71:a4 on en0 ifscope [ethernet]
192.168.124.50 (192.168.124.50) at a4:83:e7:6e:1a:f0 on en0 ifscope [ethernet]
```


##   icmp protocl 

![b2eJG6](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210918/b2eJG6.jpg)


what:  internet control message protocol
for: 检测目标 ip是否可达，以及原因


type:
1.  sucess   request,8  replay,0
2.  erro, unreachable,3  time exceed 11


error code:
1.   网络不可达代码为 `0`
2.  主机不可达代码为 `1`
3.  协议不可达代码为 `2`
4.   端口不可达代码为 `3`
5.  需要进行分片但设置了不分片位代码为 `4`

   

how:
1. dns resolve 
2. 准备icmp packet; send type=8 的请求报文；
3. 通过路由转发到指定host,
    1. 指定ip 可达，回复 type=0 回复报文
    2. 指定ip 不可达,回复 type=3的错误报文，和 error code










##  other ip question

1. localhost vs 0.0.0.0 vs 127.0.0.1
    1. 127.0.0.1:  access current host
    2. localhost is the hostname of 127.0.0.1
    3. 0.0.0.0：invalid or unkonwn target, have differnet meaning in differents context;
        1. as  a host address:  match all ipv4 address on this machine
            
            > redis 0.0.0.0:6379
        2. for client: vistit localhost
