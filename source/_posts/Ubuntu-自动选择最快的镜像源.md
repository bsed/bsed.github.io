---
title: "Ubuntu 自动选择最快的镜像源"
categories: [ "Linux" ]
tags: [ "ubuntu","mirror" ]
draft: false
slug: "ubuntu-automatically-selects-the-fastest-mirror-source"
date: "2018-11-14 19:44:00"
---

通常情况下阿里云的镜像源(`http://mirrors.aliyun.com/ubuntu/`)用得比较多

但是也不排除因网络环境导致的某个镜像源访问慢问题

那么就可以配置为自动选择镜像源进行更新

修改 `/etc/apt/sources.list` 内容


<!--more-->


以18.04为例

```bash
deb mirror://mirrors.ubuntu.com/mirrors.txt bionic main restricted universe multiverse
deb mirror://mirrors.ubuntu.com/mirrors.txt bionic-backports main restricted universe multiverse
deb mirror://mirrors.ubuntu.com/mirrors.txt bionic-proposed main restricted universe multiverse
deb mirror://mirrors.ubuntu.com/mirrors.txt bionic-security main restricted universe multiverse
deb mirror://mirrors.ubuntu.com/mirrors.txt bionic-updates main restricted universe multiverse
deb-src mirror://mirrors.ubuntu.com/mirrors.txt bionic main restricted universe multiverse
deb-src mirror://mirrors.ubuntu.com/mirrors.txt bionic-backports main restricted universe multiverse
deb-src mirror://mirrors.ubuntu.com/mirrors.txt bionic-proposed main restricted universe multiverse
deb-src mirror://mirrors.ubuntu.com/mirrors.txt bionic-security main restricted universe multiverse
deb-src mirror://mirrors.ubuntu.com/mirrors.txt bionic-updates main restricted universe multiverse
```