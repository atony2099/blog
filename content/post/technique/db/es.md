---
title: es 原理
date: "2021-01-25T14:17:23+0800"
draft: false
categories: ["elasticsearch"]
---

[Text analysis](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-overview.html#analysis-overview)

## what is  es

a distrubuted document store;

## basic concept

### 1. score

In general, **scoring in Elasticsearch** is a process to determine the relevance of retrieved documents based on user queries, term frequencies, and other important parameters

### 1. index

1. as a noun:
   index is the collections of   document

2. as a verb
the process of putting the document in the index

### 2. document

the collection of fields ;

### 3. shared

index are divided into  mutiple shared;

primary shared;
replicated share

## 1. inverted index;

in search engine, every document is  a collection  of keywords

forward index: 文档到词映射

document -> word
doc1: java, hello
doc2: hello,world

inverted index: word到文档的映射；
word  -> document
hello: doc1, doc2;
go: doc3, doc4;
you: doc5

> "hello you": doc1, doc5


## 2. how to index;

1. 



## 2. mapping

### 1. what is ?

`_mapping`

1. define field data type
2. the attribute of the filed

```js
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "message": {
        "type": "keyword",
        "ignore_above": 20 
      },
      "ad_req_id": {
                 "type": "text",
                 "fields": {
                     "keyword": {
                         "type": "keyword",
                         "ignore_above": 256
                     }
                 }
             },
    }
  }
}
```

### 2. dynamic mapping vs  explict mapping

1. dynamic: automic generate

2. explict : you specify

## 3.text analysis


the process of breaking up a string to mutiple words

1. tokenization
2. normalization

## 4. dataType

1. commom data type
1. bool
2. number
3. keyword
4. data

2. text (analyzed)

### 1. text vs keyword

1. text is analyzed; keyword isn't analyzed

  > so text lose the origin meaning,  they are not available for  sorting or aggregatition

  text can set Fielddata=true, but this  can however use significant memory.

### 2. Bucket


## query

### compound query

### 1. term query vs match query

 1. term query will analyze

## analysis

at indexed time and  query time,they use the same analyzer (normalizer)

### 1.analyzer (full text search)

![wSzkDy](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210209/wSzkDy.jpg)

analyzer:

1. tokenizer: break the strings into  secton of strings  called token

2. token filter: modify the token

   1. **Lowercase filter**

### 2. normalizers(keyword)

   [normalizer](https://www.elastic.co/guide/en/elasticsearch/reference/current/normalizer.html)

don't have tokenizer

## search

### 1. FULL text search

match, match phase ....

> The query string is processed using the same analyzer that was applied to the field during indexing.

### 2. Term query

term; exists

## DSL (domain specific languagae)

### 1. query

1. full text

2. term query

3. compound:
   1. bool:
      1. must
      2. should
      3. not

### 2. aggregation

### 1. bucket aggregation

#### 1. Term aggregation

default return top 10 terms

```
GET /_search
{
  "aggs": {
    "genres": {
      "terms": { "field": "genre" } 
    }
  }
}

```

1. bucket are dynamically built, one per unique value, by default,the `terms` aggregation will return the buckets for the top ten terms ordered by the `doc_count`

2.  

### 2. metric aggregation

## xpack

a extension  provide security,alerting,monitoring,machine learning, and many other capabilities

## index template

```json
{
  "index_patterns": ["te*", "bar*"], # match index
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "_source": {
      "enabled": false
    },
    "properties": {
      "host_name": {
        "type": "keyword"
      },
      "created_at": {
        "type": "date",
        "format": "EEE MMM dd HH:mm:ss Z yyyy"
      }
    }
  }
}
```

### 1. command

```shell
# create new template
PUT _template/template_1

#  get templates
GET _template

# delete template 

DELETE  _template/template_1
```

### 1. what is ?

   how to configure an index when it's created

### 2. the detail

1. setting: set the index
   1. number_of_shards
   2.
2. mapping: define the files
