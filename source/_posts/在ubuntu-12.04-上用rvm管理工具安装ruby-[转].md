---
title: "在ubuntu 12.04 上用rvm管理工具安装ruby [转]"
categories: [ "Linux" ]
tags: [ "ubuntu","ruby","rvm" ]
draft: false
slug: "install-ruby-on-ubuntu-1204"
date: "2013-11-29 21:58:00"
---

> 在安装octogress的时候需要ruby1.9.3, ubuntu 12.04 默认的是1.8,
> 虽然有1.9.3的源，但是所有的库用的还是1.9.1的，运行octogress的时候出现了很多莫名其妙的错误，用ubuntu自带的rvm安装更是产生无数的问题。求助于goolge,
> 发现很多讲用ubuntu做ruby on rail开发的都会手动安装rvm和最新版ruby,
> 但是跟着做了以后，总会在某个地方出现问题。最后发现了一篇[How to install Ruby (and RVM) on Ubuntu
> (for
> technotards)](http://blog.coolaj86.com/articles/installing-ruby-on-ubuntu-12-04.html),
> 终于配置成功了。

这个为了科技白痴(for technotards)写的文档算是我见过的最好的安装文档, 它并不是隐藏了大量细节只给出安装步骤，而是对很多配置和为什么这样做进行了说明，对会容易出错的细节也交代清楚。比如：在介绍了ruby的基本知识开始安装后，便说明ubuntu自带的rvm有问题，如果你已经安装过了，需要完全卸载它，并给出了需要删除的文件的具体位置。我之前手动安装rvm和ruby失败便是因为一些文件没有删除干净。甚至针对对linux并不熟悉的用户，解释如果要拷贝文档里的console命令的话，一定要拷贝命令中换行的反斜线，才能让命令执行正确。看了这个文档后，让人有一种畅快的感觉，折腾了一晚上积累的不爽一下就不见了，看来很有必要学习下怎么把文档写好。下面列出我安装时的操作，算是一个备忘
## 卸载系统默认的ruby和rvm


<!--more-->


    sudo apt-get remove --purge ruby-rvm ruby
    sudo rm -rf /usr/share/ruby-rvm /etc/rvmrc /etc/profile.d/rvm.sh
    rm -rf ~/.rvm* ~/.gem/ ~/.bundle

## 为了加快安装速度，禁止安装document

    echo 'gem: --no-rdoc --no-ri' >> ~/.gemrc

##安装必要的工具和库

    sudo apt-get install -y \
      git \
      build-essential \
      curl \
      wget

## 将rvm的配置加入.bashrc中

  ` echo "[[ -s '${HOME}/.rvm/scripts/rvm' ]] && source '${HOME}/.rvm/scripts/rvm'" >> ~/.bashrc`

## 必备插件

    sudo apt-get install -y \
      build-essential \
      openssl \
      libreadline6 \
      libreadline6-dev \
      curl \
      git-core \
      zlib1g \
      zlib1g-dev \
      libssl-dev \
      libyaml-dev \
      libxml2-dev \
      libxslt-dev \
      autoconf \
      libc6-dev \
      ncurses-dev \
      automake \
      libtool \
      bison \
      subversion \
      pkg-config \
      libgdbm-dev

安装sqlite3, ubuntu的sqlite版本太老，不支持SQLite3-FTS, 作者推荐手动安装最新版，但是我试验的结果，对octopress 并不会产生问题，所以这里仍然使用系统自带版本。

    sudo apt-get install -y \
    sqlite3 \
    libsqlite3-dev

## 安装rvm和ruby

    curl -L https://get.rvm.io | bash -s stable --ruby
    
    source ~/.rvm/scripts/rvm
    rvm reload
    rvm use default
    ruby --version 

## 安装cyaml和bundler

    gem install cyaml --no-ri --no-rdoc
    
    gem install bundler --no-ri --no-rdoc
原文：[http://zuyunfei.com/2013/04/01/install-ruby-on-ubuntu/](http://zuyunfei.com/2013/04/01/install-ruby-on-ubuntu/)

就一条命令，很简单，可能会提示没有公钥，解决方法在最后。

查看帮助和支持的版本,如果没有rvm命令请设置环境变量:

    rvm list help
    rvm list known

安装 ruby 1.9.3

先安装下依赖包：

    rvm requirements

安装ruby　1.9.3：

    rvm install 1.9.3

安装完后查看一下已经安装的版本

    rvm list rubies

使用1.9.3并设置为默认，也可以只use不加default

    rvm use 1.9.3 --default

卸载：

    rvm remove 1.9.3

可能遇到的情况：

1.提示 gpg: 无法检查签名：找不到公钥

    gpg --keyserver pgpkeys.mit.edu --recv-key  D39DC0E3

2.找不到rvm命令

设置环境变量

    source /etc/profile.d/rvm.sh
    
也可以在~/.bash_profile中添加
    
    export PATH=/usr/local/rvm/bin:/opt/git1.7.10.2/bin:$PATH



