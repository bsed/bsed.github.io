---
title: "Ubuntu下载安装Mercurial(Hg)"
categories: [ "Linux" ]
tags: [ "linux","hg","mercurial" ]
draft: false
slug: "ubuntu-download-and-install-mercurial-hg"
date: "2011-11-08 13:44:00"
---

 Mercurial 是一种轻量级分布式版本控制系统，采用 Python 语言实现，易于学习和使用，扩展性强。相对于传统的版本控制，具有如下优点：

    更轻松的管理。
    传统的版本控制系统使用集中式的 repository，一些和 repository相关的管理就只能由管理员一个人进行。由于采用了分布式的模型，Mercurial 中就没有这样的困扰，每个用户管理自己的 repository，管理员只需协调同步这些repository。
    更健壮的系统。
    分布式系统比集中式的单服务器系统更健壮，单服务器系统一旦服务器出现问题整个系统就不能运行了，分布式系统通常不会因为一两个节点而受到影响。
    对网络的依赖性更低。
    由于同步可以放在任意时刻进行，Mercurial 甚至可以离线进行管理，只需在有网络连接时同步。


<!--more-->


上述内容摘自：开源中国

`sudo apt-get install mercurial`