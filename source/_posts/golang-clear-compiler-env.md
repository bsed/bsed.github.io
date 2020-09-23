---
title: 如何清理Golang的编译环境
date: 2014-12-21 18:36:00
updated: 2014-12-21 18:38:40
tags: 
- ncdu
categories: 
- linux

---
今天编译golang代码的时候遇到这么一个问题

    [ulricqin@rmbp web]$ go build paginator.go 
    # command-line-arguments
    ./paginator.go:4: import /Users/ulricqin/code/gowork/pkg/darwin_amd64/github.com/toolkits/core.a: object is [darwin amd64 go1.3.1 X:precisestack] expected [darwin amd64 go1.3.3 X:precisestack]

paginator.go中引用了core.a，但是golang的版本不同


<!--more-->


这种问题其实很常见了，特别是你升级Golang版本之后，旧的pkg没有清理，就容易造成这个问题

解决方法也简单，干掉pkg目录重新编译即可：
`rm -rf $GOPATH/pkg `
但是我这么做了之后仍然没有解决，查了一下发现是这个原因：

我的机器上有两个golang目录，一个是1.3.1，一个是1.3.3，当时，我开着Sublime Text，安装了GoSublime插件，这个插件会即时编译golang代码，而GoSublime配置的GOROOT是1.3.1的golang目录，我在命令行下 go env 发现用到的是1.3.3的golang

所以，修改一下GoSublime的GOROOT配置，清理pkg目录即可搞定