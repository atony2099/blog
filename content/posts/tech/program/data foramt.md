---
title:  data foramt
date: 2021-01-23T18:50:23+08:00
lastmod: 2021-03-23T18:50:23+08:00
categories: ["data format"]
tags: [""]
draft: false
---

##  type

binary-foramt:  use binary encoding,  使用某种二进制格式表示数据
text-format:   use  character encoding, 使用字符表示数据 


binary format: 
1. less space， compactly
	1. 不需要冗余的符号
	2.  key可以代替


text-foramt:
1. human-readable


character-encoding:
1. ascii
2.  uft-16
3. uft-18
4. uft-16
```c
POST /api/endpoint HTTP/1.1
Host: example.com
Content-Type: application/json; charset=utf-16
Content-Length: 34

{"name":"John Doe","age":30}

```





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