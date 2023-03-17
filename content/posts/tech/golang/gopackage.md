---
title: "Go package"
date: 2020-05-14T11:39:03+08:00
draft: false
---



## 常用命令

### go get

下载对应的包和它的依赖，并调用go install安装
Get downloads the packages named by the import paths, along with their
dependencies. It then installs the named packages, like 'go install '.

1. download  way
   1. https: git clone <https://s>
   2. ssh:

 ```
 [url "ssh://git@github.com/"]
 insteadOf = https://github.com/
 ```

	git clone  ssh://git@github.com/
 
-u: 升级包和它的依赖(默认 get只会检查是否已经缺失的包，不会去安装它)

-d:

#### 源码

```
// Phase 1. Download/update.
for _, pkg := range downloadPaths(args) {
  download(pkg, nil, &stk, mode)
}

// Phase3. Install
work.InstallPackages(args, pkgs)

```

### go install

1. 编译main包并安装到 $GOPATH/bin/ 目录下
2. 编译非main包并安装到$GOPATH/pkg/ 目录下

## golng 目录

[](https://juejin.im/post/5d8ee2db6fb9a04e0b0d9c8b#heading-0)

### 1. pkg

用于链接阶段进行链接的文件。go所依赖的文件在链接阶段组合起来

## ule

### private  respons

1. change git config
2. disable proxy
 1. after:1.13
  goproxy=xxx

### abc

## goproxy

[干货满满的 Go Modules 和 goproxy.cn](https://juejin.im/post/5d8ee2db6fb9a04e0b0d9c8b#heading-15)

## what is ?

缓存packages,
