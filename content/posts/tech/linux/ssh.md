---
title: ssh
date: "2022-04-10T15:31:17+0800"
draft: false
categories: ["linux"]
---

[Understanding the SSH Encryption and Connection Process](https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process)

## what

secure shell: 加密网络协议

algorithm types:
1. RSA:
	1. -b: length 1024 to 8192 bits 
2. ED25519:  
	1. -a:  生成密钥函数的论数，论数越高，加密性越好



```shell
ssh-keygen -t [types] -C comment

ssh-keygen -t rsa -b 4096

ssh-keygen -t ed25519 -a 100

```



the structure

```shell
ssh-ed25519  # types 

AAAAC3NzaC1lZDI1NTE5AAAAIGZz3nOSfZv3bNtM1x+K5KfBdHJwaYGR0gR7x4HhF6Th user@hostname #pubic key

user@host  #public key, can change at will 
```

comment example:
```
alice@workstation
alice@workstation-alice.smith@email.com
```
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




