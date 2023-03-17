---
title: 
date: "2021-07-26T17:52:05+0800"
draft: false
categories: ["encrypt"]
---


[https://www.ruanyifeng.com/blog/2013/07/rsa_algorithm_part_two.html](RSA算法原理（二）)

[一文彻底搞懂加密、数字签名和数字证书！](https://segmentfault.com/a/1190000024523772)

##  encrypt methods

1. asymmetric encryption
    1. RSA: the main(standard)
2. symmetric  encryption


## asymetric

```
publicKey = data * dataE
privateKey = 10(data * dataE)

```
limit: high overhead;

both public key and prvate key can use to  encrypt;

but we usually use 

1. public key to send message:
   for: send symetic key;
   > if we use private key to send message, everyone have public key can decrypt it


2. generate sign: check the content is complete;
    1. hash;private key to  encrypt  the hash content
    3. decrypt the hash; check is equal 




### 1. problem;

firstly,send public key to  client;
how to check the  public key  vaild;(not replace in the middle)

1. CA:
    0. CA send private key and certification(public key) to client;


    1. server send certification with signature 

    2. brower use (CA public key) to verify the signature;








2. ssh:








## practice methoeds

1. server send public key to client; client use public key to generte session key:

2. use session key to encrypt data;




problem:
    1. how to veriry the public key is from the server



### 1. ssl

1. verify by  




### https


### ssl

