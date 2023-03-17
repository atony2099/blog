---
title: ssh
date: "2022-04-10T15:31:17+0800"
draft: false
categories: ["linux"]
---

[Understanding the SSH Encryption and Connection Process](https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process)

## what

secure shell: 加密网络协议


## how it work

1. Negotiating: get the session key 
    1. client: reqeust
    2. server: reply public key
        1. if first time, the user confirm, add to the known hosts
    3. negotiate a session key:TODO
2. authenticate: 
   1. password: 
        1. client: public key encrypt, server  decrypt
   2. ssh key paris:
      1. server encrypt data with public key,client decrypt the data with its private key;
