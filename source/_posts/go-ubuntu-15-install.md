---
title: Ubuntu15.04下安装 go 1.x
date: 2015-08-20 19:32:00
updated: 2016-06-02 11:38:33
tags: 
- php
- core-dump
categories: 
- php

---
Go 项目组发布了 Go 1.5 的计划，Go 1.5 将使用 Go 1.4 进行构建，Go 源码树将完全消除所有 C 的代码。移除 C 编译器 (5c,6c,8c,9c)。剩下的 C 程序将转成 Go 语言，包括 Go compilers ([golang.org/s/go13compiler](https://golang.org/s/go13compiler)), 汇编器和连接器 ([golang.org/s/go13linker](https://golang.org/s/go13linker)) 以及` cmd/dist`. 如果这些程序都采用 Go 编写，这会引发一个在完整构建源码的启动问题 —— 你需要一个可用的 Go 工具链来构建 Go 工具链。

##  通用安装方法

为了构建 Go 1.x （x ≥ 5），需要先安装 Go 1.4 到 $GOROOT_BOOTSTRAP. 默认的 $GOROOT_BOOTSTRAP 是 $HOME/go1.4. 我们使用 Go 1.4 作为启动的基础版本来编译工具链。
下面是我的go1.4 的配置环境 .bashrc 文件下:

### go1.4 安装

```bash
#golang1.4.3
export GOROOT=$HOME/go
export GOBIN=$GOROOT/bin
export GOPKG=$GOROOT/pkg/tool/linux_amd64
export GOARCH=amd64
export GOOS=linux
export GOPATH=$HOME/gocode
export PATH=$GOBIN:$GOPKG:$GOPATH/bin:$PATH
#export GODEBUG=schedtrace=1000
#export GODEBUG=schedtrace=1000,scheddetail=1
#alias vim='gvim'
```
我们先来看看构建 Go 1.4 是怎么样的：

    使用 gcc 或者 clang 构建 cmd/dist .

    使用 dist、build 编译器工具链（gcc 或者 clang）

    NOP


<!--more-->


    使用 dist 构建 cmd/go (as go_bootstrap)

    使用 go_bootstrap 构建其他的标准库和命令

而 Go 1.x (x ≥ 5) 的构建将变成：

    使用 Go 1.4 构建 cmd/dist

    使用 dist 构建编译器工具链

    使用 dist 重新构建编译器工具链

    使用 dist 构建 cmd/go (go_bootstrap)

    使用 go_bootstrap 构建其他标准库和命令

### go1.5 安装

 1. 先将go 1.4版本所在的 $HOME/go 目录 重命名为 go1.4
 2. 然后再将下载下来的go1.5源代码 放到 $HOME 目录下并解压到$HOME/go 目录下，进入目录并编译:

```bash
cd $HOME/go/src 
./all.bash
```
编译完成后.
将 .bashrc 文件夹下golang 环境修改成 如下所示:
```bash
#golang1.5
export GOROOT_BOOTSTRAP=$HOME/go
export GOROOT=$HOME/go
export GOBIN=$GOROOT/bin
export GOPKG=$GOROOT/pkg/tool/linux_amd64
export GOARCH=amd64
export GOOS=linux
export GOPATH=$HOME/gocode
export PATH=$GOBIN:$GOPKG:$GOPATH/bin:$PATH
#export GODEBUG=schedtrace=1000
#export GODEBUG=schedtrace=1000,scheddetail=1
#alias vim='gvim'
```
在 bsh 控制台运行 go version ,查看go环境 
```
root@gnux:~# go version
go version go1.5beta1 linux/amd64
```
### go1.6安装

先将go 1.4版本所在的 $HOME/go 目录 重命名为 go1.4
然后再将下载下来的go1.6源代码 放到 $HOME 目录下并解压到$HOME/go 目录下，进入目录并编译:
```bash
# golang1.6
export GOROOT_BOOTSTRAP=$HOME/go
export GOROOT=$HOME/go
export GOBIN=$GOROOT/bin
export GOPKG=$GOROOT/pkg/tool/linux_amd64
export GOARCH=amd64
export GOOS=linux
export GOPATH=$HOME/gocode
export PATH=$GOBIN:$GOPKG:$GOPATH/bin:$PATH
#export GODEBUG=schedtrace=1000
#export GODEBUG=schedtrace=1000,scheddetail=1
#alias vim='gvim'
```

在 bsh 控制台运行 go version ,查看go版本

     testing: warning: no tests to run
    PASS
    ok  	_/home/kelvin/go/test/bench/go1	6.680s
    
    ##### ../test
    
    ##### API check
    Go version is "go1.6rc2", ignoring -next /home/kelvin/go/api/next.txt
    
    ALL TESTS PASSED
    
    ---
    Installed Go for linux/amd64 in /home/kelvin/go
    Installed commands in /home/kelvin/go1.4/bin
    kelvin@gnux:~/go/src> go version                                (03/29 22:07:48)
    go version go1.6rc2 linux/amd64
    kelvin@gnux:~/go/src>    


## 通过Github镜像安装

### 问题

Go1.6的编译过程需要Go1.4的二进来实现 [bootstrap（自举）](https://github.com/golang/go/blob/master/doc/install-source.html) (简单来说: Go需要Go自身来编译)。

### 解决方案

 - 从[Github的Go仓库镜像](https://github.com/golang/go)获取Go的源代码。
 - 首先编译Go1.4（只需要`gcc`和`glibc-devel`，不需要Go来编译）。
 - 使用编译好的Go1.4的二进制文件来编译Go1.6。

### 步骤

如果之前已经安装过老版本的Go，清除$GOPATH, $GOROOT变量。

### 安装好Git。

 - 配置 Git
 - `git config --global user.email "email-for-github"` 
  - `git config --global user.name "user name"`
 - 创建`SSH key`，添加 `Public Key`到`Github`账号。
  - `ssh-keygen -t rsa -b 2048`
 - 复制~/.ssh/id_rsa.pub中的Public Key到github SSH settings以添加新的SSH key
 - 安装 gcc和glibc-devel
  - `sudo yum install gcc glibc-devel` (centos)
  - `sudo apt-get  install  build-essential` (ubuntu)

### 从源代码编译安装Go1.4

```bash
cd ~/
git clone git@github.com:golang/go.git
cd go
git checkout -b 1.4.3 go1.4.3
cd src
./all.bash
```

复制` ~/go` 到 `$GOROOT_BOOTSTRAP`（默认值是~/go1.4）

```bash
cp ~/go ~/go1.4 -rf
```
从源代码编译安装`Go1.6`

```bash
cd ~/go
git clean -dfx
git checkout -b 1.6 go1.6
cd src
./all.bash
```

设置 `$GOPATH` 以及添加`Go二进制路径`到 `$PATH`

```bash
sudo vi /etc/profile
# Golang1.6
export GOROOT_BOOTSTRAP=$HOME/go
export GOROOT=$HOME/go
export GOBIN=$GOROOT/bin
export GOPKG=$GOROOT/pkg/tool/linux_amd64
export GOARCH=amd64
export GOOS=linux
export GOPATH=$HOME/gocode
export PATH=$GOBIN:$GOPKG:$GOPATH/bin:$PATH
#export GODEBUG=schedtrace=1000
#export GODEBUG=schedtrace=1000,scheddetail=1
#alias vim='gvim'
```

### 重启和测试

```bash
sudo reboot
go version
go version go1.6 linux/amd64
```


  