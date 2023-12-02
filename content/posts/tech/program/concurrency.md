---
title:  concurrency
date: 2023-12-02T12:13:50+08:00
lastmod: 2023-12-02T12:13:50+08:00
categories: ["program"]
tags: [""]
---

## qps 

qps, qpm:
query per seconds/minutes: 每秒/分钟查询数  
查询：是泛指， 可以指任何类型的接口，包括get,post,put,delete

qps = total finish request count / total request time in a range  

finish rquest count: 在那个时间段接受并完成响应的请求；



qps 的估算：
1. 总的qps:
日活 1w , 每人平均120请求 ， 则 
平均 qps 120/24/3600=13 qps
峰值qps 约四倍  = 52 qps

1. 上传图片接口的qps
日活1w，每个用户平均上传一次， 
则  1w/ 24/3600= 0.1次 
峰值 四倍 =0.4



超过qps的应对策略：
1. 不做任何处理： 此时服务还能正常进行 ，只是用户等待时间边长
2. 限流：超过特定流量的请求将被拦截，牺牲部分用户



