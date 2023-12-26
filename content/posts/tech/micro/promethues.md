---
title: prometheus
date: 2021-07-24T12:29:45+0800
draft: false
categories:
  - monitor
  - micro
---


![NZTIxB](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210725/NZTIxB.jpg)


- [ ] know the basic knowledage of promethueus
- [ ] create a basic server to  monitor  redis  


## 1. what is ？
prometheus:
1. prom: 主要是存储采集的数据
2.  exporter: 采集数据
3. grafana: 前端，展示数据 


exporter:
1. 采集和转换数据
2. 不存储数据，只有 metrics 调用时候才采集 和转换





##  Time Series Data


what: 
1. 相比mysql,  只有 2-3 列， key, value, timestamp
2. 数据是基于timestamp 产生的
3. 适用于监控


Prometheus time series  data:
1. meteria, label: 相当于 key 
2. value 
3. timestamp

```
- `http_requests_total{method="GET", status="200"} 1023 @ 1670000000`
- `http_requests_total{method="POST", status="200"} 978 @ 1670000000`
```





## promql 

语法:
```
function(metric{lable})[time range ]
```

basic:
1.  request_total: 瞬时值； 当前最新的值；
2.  request_total\[5m\]: 区间值， 5m 所有上报的值


聚合操作
1. sum
2. min
3. count






##   storage

can store in a remote storag;

![PK5hG6](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210727/PK5hG6.jpg)

## config

```yml
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
    metrics_path: /metrics
    scheme: http

    static_configs:
      - targets: ["localhost:9090"]

```

metadata;

```json
{
  "discoveredLabels": {
      "__address__": "localhost:9090",
      "__metrics_path__": "/metrics",
      "__scheme__": "http",
      "job": "prometheus"
  },
  "labels": {
      "instance": "localhost:9090",
      "job": "prometheus"
  },
  "scrapePool": "prometheus",
  "scrapeUrl": "http://localhost:9090/metrics",
  "globalUrl": "http://f9cdf3cbf0ac:9090/metrics",
  "lastError": "out of bounds",
  "lastScrape": "2021-07-26T15:03:04.589192767Z",
  "lastScrapeDuration": 0.0173774,
  "health": "down"
}

```

get  http:localhost:9090/metrics;

```yml
- job_name: baidu_http2xx_probe
  params:
    module:
    - http_2xx
    target:
    - baidu.com
  metrics_path: /probe
  static_configs:
  - targets:
    - 127.0.0.1:9115
```

get  127.0.0.1:9115/probe?params=xxxx;

1. targeType:
    1. static
    2. dynamic
        1. json file
        2. dicvoery center;

- job_name:"ab"
  static_config:
  - targets:["localhost:300"]

- job_name: 'file_ds'
  file_sd_configs:
  - files:
    - targets.json

1. targets: the  

## pomethus 

指标类型

在 pomethus 数据库里存储的是 metrics  and value  and timestamp; 

api_request_count:  100;   2023-12-05 11:00:00


在客户端，可以分成几个metrics type

1. counter:  
	1. 只会增加；
	2. 使用场景:  请求数
	3. 服务重启后会归零
![VxjGyctNaWZ1](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20231223/VxjGyctNaWZ1.png)
```
request_count  1 2023-12-05 11:00:00
request_count 1 2023-12-05 12:00:00
```
1. gauge: 
	1. 可以增加和减少;
	2.  可能会少采集数据，需要设置合理间隔；
	3.  使用场景: 日常的指标 
```
temperuate  20 2023-12-05 11:00:00
temperature 15 2023-12-05 12:00:00
```
### Histogram, Summary:

使用三个metric 来暴露一组 historygram, summay

```
prometheus_tsdb_compaction_chunk_range_bucket{le="25600"} 100

prometheus_tsdb_compaction_chunk_range_bucket{le="102400"} 200

prometheus_tsdb_compaction_chunk_range_bucket{le="409600"} 300
```

![R6NfQRlUqHu6](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20231223/R6NfQRlUqHu6.png)

![okVkcWHQqouT](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20231223/okVkcWHQqouT.png)



```

request_duration_seconds_bucket{le="0.5"} 2 2023-12-05 11:00:00 request_duration_seconds_bucket{le="1.0"} 5 2023-12-05 11:00:00

```


##  使用 

1.  increse the counter 
2.  alert if the error 



## pomethus function





##  alert manager

group_wait: 30s group_interval: 5m repeat_interval: 1h

1. group_wait: 1ms: 等待同一组的其他通知1m，看是否有新的通知，有的话一起发出

2. group_interval: 5m： 两个同组的通知至少间隔  5m

1. repeat_interval: 1h:  如果持续触发通知， 则至少一小时后才再发一次



increaese(request_count) > 10\[1m\];


10:00, 触发，  10:01 等待没有新的同组通知：发出通知
持续到 10:02结束；
10:04:00 新的触发，由于 < 5m，如果持续激活 >=5:  10:07 发出通知
一直持续到 11:04：在发出通知



### alert manager config






















