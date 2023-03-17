---
title: rpc
date: "2021-06-02T17:31:49+0800"
draft: false 
tags: ["rpc","grpc"]
---
;
[Compare gRPC services with HTTP APIs](https://docs.microsoft.com/en-us/aspnet/core/grpc/comparison?view=aspnetcore-3.0)

[Use Binary Encoding Instead of JSON](https://betterprogramming.pub/use-binary-encoding-instead-of-json-dec745ec09b6)

[高效的数据压缩编码方式 Protobuf](https://halfrost.com/protobuf_encode/)

[HTTP/2 and gRPC — The Next Generation of Microservices Interactions](https://blog.netsil.com/http-2-and-grpc-the-next-generation-of-microservices-interactions-aff4ffa6faed)

[Compare gRPC services with HTTP APIs](https://docs.microsoft.com/en-us/aspnet/core/grpc/comparison?view=aspnetcore-3.0)

[Comparing API Architectural Styles: SOAP vs REST vs GraphQL vs RPC](https://www.altexsoft.com/blog/soap-vs-rest-vs-graphql-vs-rpc/)

[RPC vs REST](https://iq.opengenus.org/rpc-vs-rest/)

[RPC](https://willh.gitbook.io/build-web-application-with-golang-zhtw/08.0/08.4)

[protobuf教程(二)---核心编码原理](https://www.lixueduan.com/post/protobuf/02-encode-core/)

[Encoding](https://developers.google.com/protocol-buffers/docs/encoding)

[Schema evolution in Avro, Protocol Buffers and Thrift](https://martin.kleppmann.com/2012/12/05/schema-evolution-in-avro-protocol-buffers-thrift.html)

[Protobuf vs JSON](https://www.educba.com/protobuf-vs-json)

[gRPC Name Resolution](https://github.com/grpc/grpc/blob/master/doc/naming.md)

[Load Balancing in gRPC](https://github.com/grpc/grpc/blob/master/doc/load-balancing.md)

what:   remote procedure call

![RewVwv](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220327/RewVwv.jpg)

basic  component:
1. serilization 
2.  transport: tcp


rpc框架比较: 
![VTjDcu](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/VTjDcu.png)


why use rpc in microserve

1.  Efficiency:
	1. use binary:  samll and fast
2. safe:  based on schema, type  safety
1. advanced feature:
	1.  service discovery 
	2. load balance 
	3. streaming 




## use 



what: 
依赖于protoc 文件； 

server: grpc server,  handler
client:  grpc connect

code:  
```
// server

// 1. rpc server
grpcSrv = grpc.NewServer();
grpcSrv.Listen(tcpListen);


// 2. addRpcServer  
potocol.register(grpcSrv , handlerSrv)


// client
conn = grpc.dial()

client  = proto.newClientWithConn(conn)
client.callSomeFunc 

```



grpc.methods: 

```c
methodsTable:
	server: ....
	rpc methodname: ...
	handler: ....
```



##  restful  vs rpc 



same: 
1. client-side 
2.   transport  between process
3. based on tcp, http protocol  

differ:

1.   rpc:  
	1. rpc: action-oriented:  任何操作都相当于本地方法调用， 处理各种类型操作；
	2. http: resouce-oriented: 任何操作抽象为对资源(data,media)curd，不擅长处理非crud操作；

2. data-format:
	1. rpc: binaryformat
	2. rest:  text-format
		   
3.  advanced：
	1. 服务注册发现
	2. 负载均衡 
	3. streaming 




## protobuf


what:
1. one of date format，like json,xml  
2.  跨语言

### vs json
same:

differ:
1. encode:
	1.  text-eoncode(serilazion)
	2. binray-econde
		1. less size

2.  schema: 定义数据结构 
	1. protobuf 基于shcema, 自动校验数据，减少错误 
		1. required field list
		2.  fileld  type 
	2.  无schema， 
		




### how 


1. variant int
2.  tag  value to represent key value.



#### variant int 

使用连续位(continuation bit) 表示是否继续: first bit of the byte  

example: 1
int32: 0x00 0x00 0x00 0x01 , 4bytes
variant int32:   [ 0 ]000 0001 ,  1byte
![XhLK6RKUvOA6](https://raw.githubusercontent.com/atony2099/imgs/master/20230310/XhLK6RKUvOA6.jpg)


#### tag value

how： 
tagnumber  -> key, , length(optional), value

```c
message Person {
required string user_name        = 1;
optional int64  favourite_number = 2;
repeated string interests        = 3;
}

person.name=martion -> 01  02 06 martin

```

the used of  tag number： identify the key 


   ![sB1brB](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211123/sB1brB.jpg)

   ![N30bZc](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211123/N30bZc.jpg)






###  use  example 

file:

```
option  go_packge="github/tony/hello/proto" 
message HelloReq {
	 string  hi = 1 
	 
}

message HelloRes {
	string  ok = 1
}

service  





```

go_package  used for 
1. the import path of generted code 
2.  the  package name of  generated code




plugins:
1. protoc-gen-go: how to encode/decode;
2. protoc-gen-go-grpc: grpc client/server
   
command:
```shell
protoc  arguments   xx.proto 
```

**arguments** 

--go_out=PATH:  output direcotry;

--go_out=pahts=source_relative:.  abc/a.proto:   source proto file path+abc 

--go_out=.:  current path + go_package path

--go_out=plugins=grpc:load grpc plugin 



proto file:
1. message definition
2. server definitions


example
   ```c
	protoc  --go_out=plugins=grpc:.  proto_h5/*.proto
    
    protoc --go_out=. --go-grpc_out=.  proto_h5/*.proto
   ```




## grpc load balancer

how: 
1. resolver:  解析URL,  URL  -> ip
2.  balancer:   pick from   subConnect  


 balance strategy:  
	1. round-robin
	2. pick-first 
	3. custom


resolver:  
```go
for  addrList :=  addressChannl{
	updateConnectionList(addlist)
}
```


balancer:

```go
func Build(){
	var subConns []balancer.SubConn
	for sc := range info.ReadySCs {
		subConns = append(scs, sc)
	}
}

pickNext(){
	p.next = (p.next + 1) % len(subConns)
}
```





##  grpc advanced feature


### stream;

1. what?

   send: ---> [request message];
   receive: <- [reponse message];

   send: [requestMsg1, reqMsg2];
   response: [responseMsg, responMsg];


send mutiple reponse request/reponse at same time;
![kw8X6S](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211025/kw8X6S.jpg)


send: 
```go
func (s *server) SayHello1(in *pb.HelloRequest, gs pb.Greeter_SayHello1Server) error {
	name := in.Name
	for i := 0; i < 100; i++ {
		gs.Send(&pb.HelloReply{Message: "Hello " + name + strconv.Itoa(i)})
	}
	return nil
}
```



receive:
```go
for {
	reply, err := stream.Recv()
	if err == io.EOF {
		break
	}
	if err != nil {
		log.Printf("failed to recv: %v", err)
	}
	log.Printf("Greeting: %s", reply.Message)
}

```

2. 本质是什么？
   将一个大的请求/响应拆分成更小的请求/响应；


3. use case;

   1. 处理大量报表数据:报表数据；




## 
## other
1. encode; serilization 关系；
   same:  one format -> another format;
   differ:  serialization is a specific instace
      1. econde: one format  -> any format;
      2. serilization:  any -> array of bytes
   

2. binary-format vs textual-format;
   json: "{"number": 1}"
   1. binary-format: 非直接的字符串或者码位表示；
      > 00001 0001
   2. textual-fomat:  编码为一系列的字符或者码位
      > U+27 U+7B U+22 U+61 U+22 U+3A U+31 U+7D U+27