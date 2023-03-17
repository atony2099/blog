---
title: html
date: "2021-04-12T20:32:16+0800"
draft: false
categories: ["html"]
---
[


## the full process of html

1. dom tree
2. cssdom tree
3. render tree
   
4. lyaout  
   node:
      1. size 
      2. position
5. paint 
6. compositing


## parse block vs render block

both will block ;
1. parse-blocking: js




2. render-blocking







## encounter external resources








## how html be loaded
1. fetch html
2. parse html
3. encounter script and css;
    1. script: stop parsing; fetch js, parse and excute it 
    2. css: just continuing parsing
4.continue parsing


## 1. ready state

### 1. loading

![skn70K](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210412/skn70K.jpg)


load and parse html 

> js will breakdown the parsing

### 2. interactive

start load css, image and other resources

### 3. complete 
all resource are loaded


## 