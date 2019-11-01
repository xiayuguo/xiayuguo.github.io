---
title: Multi-Platform Compilation of Golang
date: 2018-12-28T18:30:45+08:00
draft: true
comments: false
tags: 
- Golang
---

> `Golang` 中跨平台交叉编译涉及两个重要的环境变量：`GOOS` 和 `GOARCH`，分别代表`Target Host OS` 和 `Target Host ARCH`，如果没有显式设置这些环境变量，我们通过go env可以看到go编译器眼中这两个环境变量的当前值：

```
GOARCH="amd64"
GOBIN=""
GOCACHE="/root/.cache/go-build"
GOEXE=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="linux"
GOOS="linux"
GOPATH="/home/hugo/gopath"
GOPROXY=""
GORACE=""
GOROOT="/usr/local/go"
GOTMPDIR=""
GOTOOLDIR="/usr/local/go/pkg/tool/linux_amd64
GCCGO="gccgo"
```
如果想要跨平台编译go程序，只需在build的时候制定目标主机的os和arch，如下例子所示所示
```
GOOS=windows GOARCH=386 go build -o hello.exe hello.go
```
其中二者可以设置如下参数：
```
$GOOS         $GOARCH         
darwin          386
darwin          amd64
freebsd         386
freebsd         amd64
linux           386
linux           amd64
linux           arm         incomplete
windows         386         incomplete
```
如果想要编译其他平台的可执行脚本, 只需指定相应的os和arch即可