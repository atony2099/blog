---
title: dns
date: "2021-03-22T00:47:09+0800"
draft: false
categories: ["tcp/ip"]
---

[What is DNS, How it Works + Vulnerabilities](https://www.varonis.com/blog/what-is-dns/)
[https://www.cloudflare.com/learning/dns/what-is-dns/](https://www.cloudflare.com/learning/dns/what-is-dns/)

## what is DNS?
GetIPWith(domain)

## how dns  work?

![xSXKUj](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220324/xSXKUj.jpg)
![yGH94L](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210322/yGH94L.jpg)
![5PN25l](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210918/5PN25l.jpg)



1. DNS recursive resolver(本地，递归):负责实际的查询过程: 运营商(电信，联通)，某些云服务商(谷歌)

2. 查询过程: 
    按照层级，自高向低查询(域名自右向左)；
   1. root nameserver(根域名服务器,指向顶级域名)
   2. top level nameserver(顶级域名服务器cn,com...)
   3. authoritative nameserver(权威域名服务器,存储实际ip): 域名解析商，腾讯，阿里


## DNS message format;
![ijt4jA](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220324/ijt4jA.jpg)


## command

### dig

1. answer section: the actual IP
2. AUTHORITY SECTION: the authoritative name server

## DNS recorder

A: hostname and its corresponding ip4 address
cname: alias a hostname to another hostname
