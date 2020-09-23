---
title: Ruby 下的plainsite
date: 2014-03-26 15:12:00
updated: 2015-07-19 15:12:35
tags: 
- linux
- emacs
categories: 
- linux

---
# 安装Ruby

    apt-get install ruby-dev

# 安装gem


<!--more-->


    apt-get install gem

# 修改gem源

    gem sources -l
    gem sources -r https://rubygems.org/
    gem sources -a http://ruby.taobao.org/
    gem sources -l
    plainsite

安装

    sudo gem install plain_site -V

plainsite build -l -d ~/wwwroot -r ~/gnux.cn