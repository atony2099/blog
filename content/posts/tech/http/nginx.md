---
title: nginx
date: "2020-11-29T10:17:39+0800"
draft: false
categories: ["nginx"]
---

## 1. forward proxy vs reverserd proxy

they both forward  client request;
act as an intermediary for client and server

![x3hhuw](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210426/x3hhuw.jpg)

the basic defination:

1. forward proxy:  autual client is transparent for server
   1. vpn;
   2. cache server
2. reverser proxy:  acutal server is transparent for client;
   1. nginx
   2. gateway

## 2. nginx use case

1. static server
2. reversed proxy

## 为什么不适合做网关

网关除了要服务转发外，还需要包含限流，鉴权等其他一些功能；需要进一步开发；

## nginx architecture

![](https://user-gold-cdn.xitu.io/2020/1/7/16f7e169422b9c03?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
![](https://user-gold-cdn.xitu.io/2020/1/7/16f7e1694461dc70?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

master-work;
master: manager worker
work:   handle request

## configue

### 1. nginx configure basic

```nginx


# events s
events {
  
  
}

# http modules
http{
    //**作用于所有server**
   include    /etc/nginx/proxy.conf;
   include    /etc/nginx/fastcgi.conf;
   index    index.html index.htm index.php;

    server1{
        //代表一个完整的服务(对应一个域名和端口(通常是80)) 
         server_name example.com www.example.com;
    }

    server2{
        // 默认值，处理ip直接访问
        server_name "" 

    }

    server 3{
       listen       80  default_server;
       server_name  _; //匹配所有域
    }
}

```

1. server configure

### 3. gzip compress

```nginx
http {

  gzip on;
  gzip_buffers     4 8k; # the number and size  of buffers  used to compress the response 
  gzip_comp_level 6;
 gzip_types       text/plain application/x-javascript text/css application/xml application/javascript application/json;
}
```

### 2. configure statics

```
server {
    root /www/data/blog;
    server_name 8.131.105.149;
    location / {
       
    }
}
```

### 3. proxy cache

```nginx
proxy_cache_path  /data/nginx_cache levels=1:2 keys_zone=proxycache:60m max_size=120m inactive=2h use_temp_path=off;
proxy_cache_key    $host$request_uri;
proxy_cache proxycache;


 add_header    Cache-Control  public,max-age=3600;
 proxy_ignore_headers  Cache-Control;
```

1. proxy_cache_path:
    1. keys_zone: create a shared memory zone for storing the cache key
    2. max_size: the cache max size
    3. how long an item can remain in the cache without being accessed.
