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





time="2023-12-09T01:54:31+08:00" level=error msg="Panic recovered: runtime error: invalid memory address or nil pointer dereference\nPOST /api/v1/aimoss/draw/message/page HTTP/1.1\r\nHost: api.molica.com.cn\r\nAccept: application/json\r\nAccept-Encoding: br,gzip\r\nApi-Ver: 2.0\r\nContent-Length: 65\r\nContent-Type: application/json; charset=UTF-8\r\nPlat: android\r\nUid: fbf06feef03d5858\r\nUser-Agent: molica-app/1.2.0 (android: 11 ; Xiaomi:M2102J2SC; RKQ1.200826.002 test-keys; zh-CN)\r\nVer: 1.2.0\r\nX-B3-Traceid: eee60e2f55d33920\r\nX-Common-Params: oa_id=6dbdc06df72e8008&product=molica-app&os_version=30&ip=192.168.1.6&version_code=6&channel=molica_guanfang_molica&resolution=1080*2206&platform=android&mac=02%3A00%3A00%3A00%3A00%3A00&uid=fbf06feef03d5858&version_name=1.2.0&pf=5&imei=&net=wifi&android_id=fbf06feef03d5858&brand=Xiaomi&device=m2102j2sc\r\nX-Forwarded-For: 222.129.52.132\r\nX-Forwarded-Host: api.molica.com.cn\r\nX-Forwarded-Proto: https\r\nX-Platform: app\r\nX-Token-Id: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1aWQiOjEwMjEsInVzZXJuYW1lIjoiLiIsImV4cCI6MTcxMDY5NDcyNX0.DU_DSwLeISio6-lTsIMDRdowRA-bSPeea3mE6DYYu0I\r\n\r\n\ngoroutine 54 [running]:\nruntime/debug.Stack()\n\t/usr/local/go/src/runtime/debug/stack.go:24 +0x5e\nmain.setupServer.CustomRecovery.func2.1()\n\t/builds/backend/molica/utils/middleware/logger.go:74 +0xea\npanic({0x130dd00?, 0x2173580?})\n\t/usr/local/go/src/runtime/panic.go:914 +0x21f\nxgit.molica.com.cn/backend/molica/domain.DrawChatMessageToResp(0xc000f4cd80, 0x0)\n\t/builds/backend/molica/domain/draw.go:419 +0xf5\nxgit.molica.com.cn/backend/molica/usecase.(*DrawService).PageDrawChatMessage(0xc000602bd0, {0x175b658, 0xc000519000}, 0xc000a3fc20)\n\t/builds/backend/molica/usecase/draw_service.go:835 +0x165\nxgit.molica.com.cn/backend/molica/controller.(*DrawController).PageDrawChatMessage(0xc000292400, 0xc000519098?)\n\t/builds/backend/molica/controller/draw_ctl.go:167 +0x72\ngithub.com/gin-gonic/gin.(*Context).Next(0xc000519000)\n\t/go/pkg/mod/github.com/gin-gonic/gin@v1.9.1/context.go:174 +0x2b\nxgit.molica.com.cn/backend/molica/routers.SetupRouter.JWTAuth.func2(0xc000519098?)\n\t/builds/backend/molica/utils/middleware/jwtauth.go:89 +0x257\ngithub.com/gin-gonic/gin.(*Context).Next(0xc000519000)\n\t/go/pkg/mod/github.com/gin-gonic/gin@v1.9.1/context.go:174 +0x2b\nmain.setupServer.CustomRecovery.func2(0xc000519098?)\n\t/builds/backend/molica/utils/middleware/logger.go:81 +0x45\ngithub.com/gin-gonic/gin.(*Context).Next(0xc000519000)\n\t/go/pkg/mod/github.com/gin-gonic/gin@v1.9.1/context.go:174 +0x2b\nmain.setupServer.LoggingMiddleware.func1(0xc000519000)\n\t/builds/backend/molica/utils/middleware/logger.go:29 +0x9a\ngithub.com/gin-gonic/gin.(*Context).Next(...)\n\t/go/pkg/mod/github.com/gin-gonic/gin@v1.9.1/context.go:174\ngithub.com/gin-gonic/gin.(*Engine).handleHTTPRequest(0xc000503520, 0xc000519000)\n\t/go/pkg/mod/github.com/gin-gonic/gin@v1.9.1/gin.go:620 +0x65b\ngithub.com/gin-gonic/gin.(*Engine).ServeHTTP(0xc000503520, {0x1759470?, 0xc000a64460}, 0xc000fab100)\n\t/go/pkg/mod/github.com/gin-gonic/gin@v1.9.1/gin.go:576 +0x1dd\nnet/http.serverHandler.ServeHTTP({0x1755cb0?}, {0x1759470?, 0xc000a64460?}, 0x6?)\n\t/usr/local/go/src/net/http/server.go:2938 +0x8e\nnet/http.(*conn).serve(0xc0000c42d0, {0x175b6c8, 0xc00011e570})\n\t/usr/local/go/src/net/http/server.go:2009 +0x5f4\ncreated by net/http.(*Server).Serve in goroutine 53\n\t/usr/local/go/src/net/http/server.go:3086 +0x5cb\n" traceID= userID=0