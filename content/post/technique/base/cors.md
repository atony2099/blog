---
title: 
date: "2021-01-19T20:45:47+0800"
draft: false
categories: ["Go"]
---
[Cross-domain Ajax with Cross-Origin Resource Sharing](https://humanwhocodes.com/blog/2010/05/25/cross-domain-ajax-with-cross-origin-resource-sharing/)

## cors 
cross-origin resource sharing

cors is an http based meachamism that allow a server to indicate that any other origin 




 cors is a w3c draft define  how the brower and the server communicate when accessing resource origin;



## how to do?



### 1. simple request


#### 1. requirements

1. One of the allowed methods:GET;HEAD;POST
2. the only headers which are allowed to be manually set are
   - Accept
   - Accept-Language
   - Content-Language
   - Content-Type (but note the additional requirements below) 
3. The only allowed values for the Content-Type header are:
application/x-www-form-urlencoded
multipart/form-data
text/plain


#### 2. how 

1. brower send request with  origin header(origin:baidu.com)
2. server reply with Access-Control-Allow-Origin  header(Acess-Control-Allow-Origin:baidu.com )




### 2. preflight request

#### 1. requirements
not simple request


#### 2.  how 

1. prefilight
   
   1.  brower send request
         1. Origin – same as in simple requests. 
         2. Access-Control-Request-Method – the method that the request wants to use.
         3. Access-Control-Request-Headers – (Optional) a comma separated list of the custom headers being used.
        > Origin: https://humanwhocodes.com
           Access-Control-Request-Method: POST
           Access-Control-Request-Headers: NCZ


    2. brower send respose
       1. Access-Control-Allow-Origin – same as in simple requests.
       2. Access-Control-Allow-Methods – a comma separated list of allowed methods.
       3. Access-Control-Allow-Headers – a comma separated list of headers that the server will allow.
       4. Access-Control-Max-Age – the amount of time in seconds that this preflight request should be cached for.

       > Access-Control-Allow-Origin: https://humanwhocodes.com
          Access-Control-Allow-Methods: POST, GET
          Access-Control-Allow-Headers: NCZ
          Access-Control-Max-Age: 1728000
