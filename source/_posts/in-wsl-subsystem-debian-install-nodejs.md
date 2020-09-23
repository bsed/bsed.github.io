---
title: 在 WSL 子系统 debian 下 install nodejs
date: 2019-07-12 16:29:00
updated: 2019-07-12 16:29:45
tags: 
- rust
- 标准库
- ffi
categories: 
- rust

---
## 第一步
```bash
$ sudo apt update
$ sudo apt upgrade
```
## 第二步
```bash
$ sudo apt install build-essential
```


<!--more-->


### 编译安装 zlib-1.2.11
```bash
$ wget http://www.zlib.net/zlib-1.2.11.tar.gz
$ tar -xvf zlib-1.2.11.tar.gz
$ cd zlib-1.2.11
$ ./configure
$ make
$ sudo make install
```

### 编译安装 python2.7

```bash
$ wget https://www.python.org/ftp/python/2.7.15/Python-2.7.15.tgz
$ tar -xvf Python-2.7.15.tgz
$ cd Python-2.7.15
$ ./configure
$ make
$ sudo make install
```
## 第三步

```bash
$ sudo apt install git
$ git clone https://github.com/nodejs/node.git
$ cd node
$ ./configure
$ make && sudo make install
```