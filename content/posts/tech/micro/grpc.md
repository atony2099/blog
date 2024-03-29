---
title: rpc
date: 2021-06-02T17:31:49+0800
draft: false
tags:
  - rpc
  - grpc
lastmod: 2024-01-13
---
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

what:   remote procedure call, used for communicate. between process 

![RewVwv](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220327/RewVwv.jpg)

basic  component:
1. serilization 
2.  transport: tcp


rpc框架比较: 
![VTjDcu](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/VTjDcu.png)



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
3. based on http, tcp 

differ:

1.  function vs resouce: 抽象方式 
	1. rpc: action(function)-oriented:  operatioon= call local funtion   处理各种类型操作；
	2. http: resouce-oriented:  operaiton= curd resource, 不擅长处理非crud操作

3. data-format:
	1. rpc: binaryformat
	2. rest:  text-format
3.  advanced：
	1. 服务注册发现
	2. 负载均衡 
	3. streaming 



why use rpc in microserve:  

1.  fast: binary foramt, less size.
3.  more safe:  schema, type safety
5. more advanced:
	1. code generation 
	2. service discovery 
	3. load balance 
	4. streaming 




## protobuf

what:
1. one of date format，like json,xml  
2.  跨语言



### vs json

differ:
1. encode:
	1.  text-eoncode(serilazion)
	2. binray-econde : lesss size
2. type safety: 基于 schema实现type check, avoid unexpected behavior


### how 

1. variant int
2.  Tag  value to represent key value.

#### variant int 

what:
使用最高位表示是否有更多byte
1: continue 
0: end   


pros:   use less space when int is small; 


example: 1
![XhLK6RKUvOA6](https://raw.githubusercontent.com/atony2099/imgs/master/20230310/XhLK6RKUvOA6.jpg)


#### tag value

how： 
tagnumber  -> key, length(optional), value

```c
message Person {
required string user_name        = 1;
optional int64  favourite_number = 2;
repeated string interests        = 3;
}

person.name=martion -> 01  02 06 martin

```

the used of  tag number： identify the key 
![7IoPTF4RT7it](https://raw.githubusercontent.com/atony2099/imgs/master/20230327/7IoPTF4RT7it.png)

 
![N30bZc](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211123/N30bZc.jpg)



can grpc number duplicate:
no, key  is represent in a unique key number during encoding and decoding 



## protobuf


```protobuf
syntax = "proto3";

package hello_123;

  

option go_package ="xgit.molica.com.cn/backend/hello;hello";

  

service HelloWorldService {

rpc SayHello(HelloWorldRequest) returns (HelloWorldResponse) {}

}

  

message HelloWorldRequest {}

  

message HelloWorldResponse {

string message = 1;

}



```


package: use in internal space, 

##  how to use it


###  define 


structure
package: namespace
service:  a set of methods  
```
package aa


message xx {

}

service  xxx  {

}

```

convention:
1.   PascalCase for  message name:  
2.  snake  case     name  file   file 
3.  PascalCase for  service name and  method 

```protoc





message HelloReq {
	string song_name = 1;
}



message  HelloRes  {
	string  age int32 = 1;
}


service Greeter {
	rpc  SayHello (HelloReq) returns (HelloRes)
}

```



### command 

plugins:
1. protoc-gen-go: how to encode/decode;
2. protoc-gen-go-grpc: grpc client/server
   
command:

simple: 
```shell
protoc  --go_out   xx.proto 
```

```shell
protoc --go_out=paths=source_relative:.  --go-grpc_out=paths=source_relative:. -Iapi  hello/v1/hello.proto

protoc --proto_path=api  --go_out=. --go_opt=paths=source_relative    --go-grpc_out=. \
--go-grpc_opt=paths=source_relative  hello/v1/hello.proto

```

protoc --go_out=paths=source_relative:api  api/hello/hello.proto

**arguments**: 

--go_out=PATH:  output direcotry;
--go_out=pahts=source_relative/import:.
the output directory is:
1. import: import directroy
2.  source_relative:  the proto file relative directory




###  grpc and http server 



server:  bind grpc service and   protocServer 
```go

grpcSer := grpc.NewServer();

helloProtco.Register(grpcSer, protocServer)

grpcServer.Serve(tcpConnect)


```

client:  bind  serverClient  

```


serverClient.Register(tcpConnect)




```



##  load balancer

default  methods for grpc



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




## grpc serverr

1. use   protoc to generate the necesssary file
2.  generate  the   server and client;


server:
```
grpc Server = grpc server

grpcSev.listen(tcp);
rpcHandler.register(grpcSev)

```

client: 
```


```



