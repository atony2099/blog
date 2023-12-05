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
3. grafana: 从prom 检索数据，进行展示  


### storage

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
