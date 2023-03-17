---
title: tcp protocol
date: "2020-12-06T18:17:23+0800"
draft: false
tags: ["tcp/ip","io"]
---

[30张图解： TCP 重传、滑动窗口、流量控制、拥塞控制](https://www.cnblogs.com/xiaolincoding/p/12732052.html)

[TCP 的那些事儿（上）](https://coolshell.cn/articles/11564.html#TCP%E5%A4%B4%E6%A0%BC%E5%BC%8F)

[SYN packet handling in the wild](https://blog.cloudflare.com/syn-packet-handling-in-the-wild/)

[linux网络编程之TCP/IP基础（四）：TCP连接的建立和断开、滑动窗口](https://blog.csdn.net/u010944926/article/details/20446149)

[How Linux creates sockets and counts them](https://ops.tips/blog/how-linux-creates-sockets/)

[小菜学编程](https://fasionchan.com/network/icmp/icmp/)

[TCP Send Window, Receive Window, and How it Works](https://cabulous.medium.com/tcp-send-window-receive-window-and-how-it-works-8629a4fad9ec)

[ppt](https://slideplayer.com/slide/11562674/)

[TCP Sliding Window Data Transfer and Acknowledgement Mechanics](http://www.tcpipguide.com/free/t_TCPSlidingWindowDataTransferandAcknowledgementMech-2.htm)

[TCP的快速重传机制](https://www.jianshu.com/p/62940de97ca5)

[为什么 TCP 协议有粘包问题](https://draveness.me/whys-the-design-tcp-message-frame/)

[TCP Socket Listen: A Tale of Two Queues (2022)](http://arthurchiao.art/blog/tcp-listen-a-tale-of-two-queues/#11-why-listen-queues)

[ How to compute DevRTT, estimated RTT, & time-out interval in CCN](https://www.educative.io/answers/how-to-compute-devrtt-estimated-rtt-time-out-interval-in-ccn)

[字节一面：TCP 和 UDP 可以使用同一个端口吗？](https://www.51cto.com/article/714728.html)

[What is the meaning of SO_REUSEADDR (setsockopt option) - Linux](https://stackoverflow.com/questions/3229860/what-is-the-meaning-of-so-reuseaddr-setsockopt-option-linux)

[TCP keepalive overview](https://tldp.org/HOWTO/TCP-Keepalive-HOWTO/overview.html)
## tcp  header  &&  status

### header

![cInHHW](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210628/cInHHW.png)

```shell
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        Sequence Number                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Acknowledgement Number                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Data |           |U|A|P|R|S|F|                               |
| Offset| Reserved  |R|C|S|S|Y|I|            Window             |
|       |           |G|K|H|T|N|N|                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Checksum            |         Urgent Pointer        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options                    |    Padding    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                             Data                              |

```

size: 20bytes ~ 60bytes(40 bytes options)

1. port: source or destination; 2^16;
2. number:sequence and acknowledge number；
3. flags
   1. SYN
   2. ACK
   3. FIN
   4. PST: push
   5. RST:reset the connection
   6. URG: urgent data (priority data)


client  create connection port range:  
```c
net.ipv4.ip_local_port_range

```

61000 - 32768 = 28232


### tcp  status 

![FtGwAd](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/FtGwAd.jpg)


server status:
listen:  passive  open

client status:
sync-sent:   ative open





###  tcp tuple 


tcp tuple:
tcp use (srcip,srcport, desip, des) as key to identifyu  a socket

server bind same port for tcp and udp: 
yes,不同协议，不会冲突
![0xBWTQ](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/0xBWTQ.jpg)

为什么重启服务，报错Address already in use:
重启时，服务器发起close,需要等待 2MSL 时间才能完全关闭connection, 持续占用ip+port 

客户端time_wait耗尽端口:
当客户端对同一个server 发起请求后又迅速关闭conmnection
1. 大量关闭请求在time_wait, port被占用




## tcp vs udp


tcp vs udp: 
1. more  reliable: tcp
2. more fast:  udp

reliable: 
1.  connection-oriented 
	1. 握手建立连接
	2.  消息确认机制
2. error- check 
	1. checksum
	2.  超时重传
3.  流量调节
	1. 流量控制
	2. 拥塞控制





##   three-way handshake

![YkTrxX](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211208/YkTrxX.jpg)


process:
1. sync:    syncX
2. ack+sync:   ackX+1, syncY
3. ack: ackY+1

random sequeuece number: decrease attack
有效的 ack = syncNum+ 1

可以从服务端发起握手吗:
技术上可行,  需要 公网ip + port, 客户端一般没有公网ip 



###  handshake server 

![aowtUk](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/aowtUk.png)

![wI9KlY](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210614/wI9KlY.jpg)

![RiKvT7](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/RiKvT7.jpg)



tcp server:
1. listen  state:   passive open
2. three  handshake: 
	1. receive  syn:   create   req socket 
	2.  receive  ack:  move  socket to accept queue
3.   accept:  dequeue socket  from accept queue
4. read:   read from   accepted  socket

code
```c
address := localhost+pport 
listFD = new socket();
bind(listenFD, address)
listen(listenFD); 
while(1){
   // get ready conne
   acceptFd =  accept(listenFD);  //
   recv(accetpFd, To: appBuffer);
   close(acceptFd)
}

```
syn flood attac:  syncQueue.length > maxLenght, 导致其他请求无法连接
why:  client故意不完成握手(发起syn 但不回复ack)


##   four-way terminate
![AMp1x1](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210919/AMp1x1.jpg)
![xwQo9K](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211208/xwQo9K.jpg)

process:
1. active: FinX
2. passive:  ACKX+1
3. passive:  FinY
4. active: ACK  Y+1, 等待2MSL 

2MSL:
max segement lifetime, 报文传输时间的最大值
```
 sysctl net.ipv4.tcp_fin_timeout
```
等待2msl 后才关闭连接 , 在此期间
1. socket无法被复用
2. delay packet将被di


why 2MSL:   
1.   确保能重传ack 从而 关闭 被动方
2.  确保排干本次连接延迟的包

 为什么挥手需要四次:
 1. 完整的握手四次
 2. 握手阶段合并ack 和 syn
 3. 挥手的时候无法合并


##  ack  and retransmission


### ack 
how:
1. sender:  seq x,  datasize=n
2. receiver: ack x+n


###  Retransmission  

what:
当包丢失的时候，触发重传机制

#### retransmit timeout

![egVgZG](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211208/egVgZG.jpg)


RTT: round-trip time,  往返时间
smooth RTT: 平滑 RTT，加权平均RTT
deviatedf RTT:  加权平均偏差 RTT偏差 
smaple RTT: 采样RTT,  上一次 RTT

RTTs(somthed RTT) = (7/8) * old_RTTs  + (1/8)RTTSample;
RTTd =    (3/4) * old_RTTd * 1/4(RTTSample-RTTs)
RTO= RTTs + 4RTTd 

feature:
1. 能根据网络状况动态调整， 网络越差， 超时时间越长 


####  fast retransmit 

how: 
sender:  重复ack 最后一个 有序的包
receiver: 连续收到 三个 重复的ack 会 重传丢失的包  

problem: 
不知道完整的丢失包
for example :  
sender: seq 1,2,3,4 
receive:  
1. receiv: 1, 4;
2. ack: 2,2,2,  sender只知道seq2包已丢失，sender只发送seq包
3. then ack: 3,3,3, sender 再发送 seq3包

![B1eDYL](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211208/B1eDYL.jpg)

![DGqDpk](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211020/DGqDpk.jpg)



####   selective Ack

![0Il6v5](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211208/0Il6v5.jpg)

fast transmit + sack: 通过 sack告知已经收到的包

## flow control

![hEA6K8](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210919/hEA6K8.jpg)
![XBhgQN](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210919/XBhgQN.jpg)
![NKccvH](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210919/NKccvH.jpg)
![rhkHTN](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210919/rhkHTN.jpg)

![IaPwme](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210919/IaPwme.jpg)

![9KOAyO](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210919/9KOAyO.jpg)



what:
发送方根据接收方的接受能力调整发送量；
最大发送量= size of slide window，超过窗口大小，停止发送


window change:
1. sender: 
	1. window = unack +  toBeSended 
	2.  buffer = window + spare 
2. receiver: 
	1.  window = rec.Next + windowSize
	2.  buffer =    unread + window  +spare 
if unack incrase, sender   window decreae
if  receiver window decrease, sender window decreae


receiver window decrease: unread data increase, window decrease 

example:
![dIFBkr](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/dIFBkr.jpg)




### window problem 

zero windw  and lost  ack:
```
when windowsize==0
	startProbe:
		getWindowSize 
```


sillly winow:  small packet problem,   持续的发送小包，没有充分利用带宽

solve: 
1. sender：
```c
if windowSize <= mss || buffer/2, windowSize =0
```
4. receiver: 累计足够的size 再发送

```c
if  toBeSendDta >= mss, sent
if  unackDta==0, sent 
```


## congestion control

what:
发送方根据网络情况动态调整发送量；

![pqBnMK](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/pqBnMK.jpg)

how:
maxData<=  congestion window


types:
1. 慢启动: 窗口指数增加
2. 拥塞避免: 窗口线性增加
3. 拥塞发生
4. 快速恢复



## tcp  keep alive

what: 通过发送探测包确保tcp连接alive的机制

tcp非正常关闭:
1.   对方节点crash 
2.   被proxy 关闭, delete from connection table, and drop packet 

![VA08Ln](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/VA08Ln.png)

![ESn3a0](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/ESn3a0.png)

configs:
1. tcp_keepalive_time: 7200
2. tcp_keepalive_probes: 9
3. tcp_keepalive_intvl:  75


how:
1.  If the connection is silent for `tcp_keepalive_time` seconds, send a single empty `ACK` packet
2.  Did the server respond with a corresponding `ACK` of its own?
    -   **No**
        1.  Wait `tcp_keepalive_intvl` seconds, then send another `ACK`
        2.  Repeat until the number of `ACK` probes that have been sent equals `tcp_keepalive_probes`.
        3.  If no response has been received at this point, send a `RST` and terminate the connection.
    -   **Yes**: Return to step 1


## 数据包拆和黏包
### divide;
#### 1. how 

data  must   divide  into  N  block;     the data is limited by phyical trasmit size; 

tcp layer:  

​	Get MSS(max segement size)  By handshake; Dymanic

ip layer: 

​	 Get  MTU(Maximum transmission unit) By  Mtu discovery;  Dymanic 



#### tcp

userData Nsize:
	1. tcp segement:    {segement1...segement2| segement.size=mss, }
	2. Ip datagram:{datagram1...datagramN | datagram.size <= mtu}

###  黏包(sticky)

1. what ?

   get image1,image2,image3, 复用同一个请求；

   数据流:    111112222233333;

   一个连接里存在多个请求的数据 ，用户端如何知道这个数据长度(边界)从而正确读取数据；

2. how to resolve
    1. 元数据指定大小；

       > content-length: NK;
       >
       > Read(fd, Lenght: N);

      
    2. 终结符(delimiter): 

       	1111\02222\0 ;



