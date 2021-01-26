---
title: "Ruby 下的plainsite"
categories: [ "Rails" ]
tags: [ "ruby","plainsite" ]
draft: false
slug: "ruby-under-plainsite"
date: "2014-03-26 15:12:00"
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