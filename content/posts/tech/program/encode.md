---
title: character encode
date: "2021-11-08T19:19:56+0800"
draft: true
categories: ["base "]
---

[Unicode](https://en.wikipedia.org/wiki/Unicode)


## what?
1. character encoding 
   assign  code point to characters

2. the range of code point
   | Plane     | Allocated code points | Assigned characters  |
   | :-------- | :-------------------- | -------------------- |
   | 0 BMP     | 65,520                | 55,632               |
   | 1 SMP     | 25,696                | 22,982               |
   | 2 SIP     | 60,912                | 60,872               |
   | 3 TIP     | 4,944                 | 4,939                |
   | 14 SSP    | 368                   | 337                  |
   | 15 SPUA-A | 65,536                | none (by definition) |
   | 16 SPUA-B | 65,536                | none (by definition) |
   | Totals    | 288,512               | 144,762              |



3. types:
   1. BMP: basic plane, 0 ~2^16
   2. other plane

4. encoding
   how to represent code point;
   uft-8: 1-4 bytes
   uft-16: 2,4 bytes
   utf-32: 4 bytes





