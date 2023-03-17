---
title: yum&&rpm
date: "2021-05-04T14:46:45+0800"
draft: false
categories: ["linux"]
---
## 1. what is yum and rpm

rpm is redhat package manager
yum: yellow dog updater,modified

same:
    they both package manage

difference:
rpm in low level;

yum in high level; yum can resolve dependency

```
========================
 Package                       Arch        Version                    Repository      Size
=============================================================
Installing:
 java-11-openjdk-devel         x86_64      1:11.0.11.0.9-2.el8_4      
Installing dependencies:
 adwaita-cursor-theme          noarch      3.28.0-2.el8               appstream      647 k
```

## rpm repository

```shell
[elasticsearch-7.x]
name=Elasticsearch repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```

### gpg

1. what is gpg?
   Gnu privacy guard; contain  publicKeys
2. gpgKey
   gpgKey  is  a public key to verify package;
3. gpkey command

   ```
    rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
   ```

## 2. how to install  repo

### 1.install by yum

> auto install dependency

1. create repo file：  in  /et/yum.repos.d directly

   ```shell
   [mongodb-org-4.4]
   name=MongoDB Repository
   baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.4/x86_64/
   gpgcheck=1
   enabled=1
   gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
   ```

2. instal by yum

```shell
yum install <package>
```

### 2. install by rpm

```
wget xxxx.rpm
rpm rpm -ivh  <package>
```

## 3.yum command

```shell
yum repolist # list all repo

yum install
yum reinstall
yum remove
yum update
yum search
yum  list

yum list | less
yum list installed | less
```


1. update vs upgrade:
   1. same: update packages to the lastes version;
   2. differ:  upgrade = update --obsolete
      --obsolete: remove the obsolete depency;


2. apt update vs yum update:
   apt update: only refresh local index files,not actual  update a package;

