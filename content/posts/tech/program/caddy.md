---
title: caddy
date: 2023-12-08T14:45:56+08:00
lastmod: 2023-12-08T14:45:56+08:00
categories:
  - program
tags:
  - caddy
---

[Caddyfile Concepts — Caddy Documentation - https://caddyserver.com/docs/caddyfile/concepts](https://caddyserver.com/docs/caddyfile/concepts)


![sYXNuyCjRKLl](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20231208/sYXNuyCjRKLl.png)

## config 






### servers

server's global configure
```caddyfile
{
    servers {
        timeouts {
            read 30s
            write 30s
            idle 5m
        }
        max_header_size 8192
        protocol {
            allow_h2c
            experimental_http3
        }
    }
}
```

