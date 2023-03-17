---
title: openvpn
date: "2021-04-19T02:07:09+0800"
draft: false
categories: ["dev"]
---


## openvpn






## openvpn config 

```shell
local 0.0.0.0
port 1184
local 0.0.0.0
port 1184
proto tcp
dev tun
ca /data/openvpn/conf/keys/ca.crt
cert /data/openvpn/conf/keys/vpnserver.crt
key /data/openvpn/conf/keys/vpnserver.key  # This file should be kept secret
dh /data/openvpn/conf/keys/dh2048.pem
server 10.7.0.0 255.255.255.0
ifconfig-pool-persist ipp.txt
push "route 192.168.0.0 255.255.0.0"     #<E8><B7><AF><E7><94><B1>ip<E8><A6><81><E6><A0><B9><E6><8D><AE><E6><9C><AC><E6><9C><BA>ip<E5><81><9A><E5><8F><82><E8><80><83>
client-to-client
keepalive 10 120
cipher AES-256-CBC
comp-lzo
persist-key
persist-tun
status openvpn-status.log
log-append  openvpn.log
verb 3
--script-security 3
auth-user-pass-verify /data/openvpn/conf/checkpsw.sh via-env
client-cert-not-required
username-as-common-name
```

## openvpn configure
1. server 
   1. authority cert
   2. key, cert;
   3. dh
2. client 


## what is openvpn