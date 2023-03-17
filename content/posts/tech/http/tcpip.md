---
title: overview
date: "2021-06-28T16:10:34+0800"
draft: false
tags: ["tcp/ip"]
---

[如何理解OSI或TCP/IP协议的层次划分和结构 ? - 向往美的回答 - 知乎](https://www.zhihu.com/question/55990047/answer/196457064)

[Bandwidth Vs. Latency: Which Is More Important?](https://www.volico.com/bandwidth-vs-latency-which-is-more-important/)

## overview

what:
分层协议，解决如何在互联网传输数据

history:
1. IETF(前身国防部) 指定ip层以上相关协议 
	1. key rfc:  rfc 1122, define layer; rfc 2616, define http1.1
2. IEEE管指定 物理层和链路层的协议


![4Htwj1](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210812/4Htwj1.jpg)
![iM1IvV](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210812/iM1IvV.jpg)
![kBdOVC](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210812/kBdOVC.jpg)
![nQuyM2](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210613/nQuyM2.jpg)
![9tArVc](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210812/9tArVc.jpg)
![img](https://sleepy-1256633542.cos.ap-beijing.myqcloud.com/20181224165918.png)




##  layer detail 

osi vs tcp/ip: 
1. tcp/ip: 实际使用的模型，更简化
2. osi: 概念模型，未被实现

![8RqYih](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210711/8RqYih.jpg)


tcp/ip  layers:
 ![0Z2jrr](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211208/0Z2jrr.jpg)
1. linker layer
2. internet layer  
3. transport layer: 
4. application layer 

链路层(linker layer): 
局域网内如何传输
protocol list: mac 

internet layer:
网路层如何路由
protocol  list: ip 

transport layer:
点对点传输
protocol list:tcp,udp

application layer:
应用相关数据
protocol:  ethernet






##   What happens when you type a URL in the browser


how: 
1. DNS resolve: GET IP
2. shake: tcp and ssl shake 
3. client: send data with http format
4. sever: send response with http format
5. browser: parse data and display

how transport by ip:
	


2. blow transport
      1. arp protocol  get ip->mac address
      2. liner: send by  mac address




## command
1. ifconfig
   lo: virtual network interface that your computer use to communicate with itself;

2. tcpdump
   inspect netwrok
   ```shell
    tcpdump -# -S -n -i lo0 tcp and host 127.0.0.1 and port 8000
    tcpdump -nl eth0
   
    tcpdump -eni any ip
   ```
   1. -#: print a optinal package number
   2. -S: print the absolute sequence number
   3. -n: don't convert address to names
   4. -i:interface
   5. -e: Print the link-level header on each  dump  line
   6. 4. expression: xxx and xxx

3. nc:
   create or connect tcp/udp connection
   1. nc -l: create a listener
   2. nc localhost 8000: connect the address







## Test

![kWMSWM](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220412/kWMSWM.jpg)

1. lantency vs bandwith
   1. lantency:  a packet round-trip time，两点之间距离,ms，
      > 主要是客观距离因素造成，交钱也无法提高
   2. bandwidth: mbps, total data in  a second, 类似单位时间人流量，管道的横街面积； mbps;
      > 运营商限制了，可以交钱提高


2. affect:
   1. lantency important in:  live, game, videochat， 需要实时响应
   2. bandwidth in : movies; 持续大量传输数据


3. in browsering 
   1. low bandwidth and low  lantency: 初始立即有内容，但页面加载慢
   2. high lantency and high bandwidth: 初始白屏时间长，后续流畅；

   





## basic information

### 1. router

    forward data  between different networks
### 2. gateway
    a router provide access for packets  of local network
    gatway must be a router;





## mtu 


![Yic8xT](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/Yic8xT.jpg)


MTU: max transfer unit 
MSS: max segment size:  1460


mss announcementL: