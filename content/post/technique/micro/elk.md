---
title: elk
date: "2021-04-30T12:49:18+0800"
draft: false
categories: ["micro"]
---

## what is elk

![HzIaTt](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210507/HzIaTt.jpg)

![u4Byza](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210720/u4Byza.jpg)

![GqPeyT](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210720/GqPeyT.jpg)

roles:

1. logstash:
   1. ingest datas;
   2. transform it
   3. send it to a stash

2. beat:
    data collection

3. elasticsearch: store data;

## logstash

### 1. collection data configure

```shell
# ingest data
input{

}
# transform  data
filter{

}


# data go to where
output{
    
}

```

1. grok:
   1.translate  plain text  to structure data;
   2. add,remove field
filter example

```
filter {
   grok {
    match => { "message" => ['%{TIMESTAMP_ISO8601:time} %{LOGLEVEL:logLevel} %{GREEDYDATA:logMessage}'] }
  }
}
   ```

### 2. output

```conf
output {
  elasticsearch {
    hosts => "172.16.7.31:10200"
    index => "logstash-nginx-ingress-logs-%{+YYYY.MM.dd}"
    

    manage_template =>  true,
    template => "/data/logstash-7.4.0/config/template.json"
    template_name => "template"
  }
 }
```

1. hosts: the stash host
2. index: index name
3. template
    1. manage_template: true
        1. false: only use api to create template
        2. true: create a template on es  on logstah's startup
    2. template: custom template path
    3. template_name: how the template is named
    4. template_overwrite: overwrite the template if one exists already under the same name;

## beat and input config

consume a specific source

1. filebeat
1. consume file

   ```shell
   filebeat.inputs:
   - input_type: log
     paths:
       - /var/log/httpd/access.log

   document_type: apache-access
   fields_under_root: true

   output.logstash:
     hosts: ["127.0.0.1:5044"]
   ```

```
input {
  beats {
    port => 5044
  }
 }

filter {
  grok {
    match => { "message" => "%{IPORHOST:clientip} %{USER:ident} %{USER:auth} \[%{HTTPDATE:time}\] "(?:%{WORD:verb} %{NOTSPACE:request}(?: HTTP/%{NUMBER:httpversion})?|%{DATA:rawrequest})" %{NUMBER:response} (?:%{NUMBER:bytes}|-)" }
  }
  date {
    match => [ "timestamp" , "dd/MMM/yyyy:HH:mm:ss Z" ]
  }
 }

output {
  elasticsearch { hosts => ["localhost:9200"] }
  }
```
