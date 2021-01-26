---
title: "TIL - Vagrant and Unit testing"
categories: [ "JS" ]
tags: [ "vagrant","unitest" ]
draft: false
slug: "vagrant-and-unit-testing-til"
date: "2015-10-06 19:36:00"
---

> 原文：[http://notes.iissnan.com/2015/reading-list-2015-02-03/]

今日阅读的文章内容主要包含两个部分，一个是[Vagrant](https://www.vagrantup.com/)，另外一个是`JavaScript`的单元测试相关的知识。Vagrant的内容涉及两个方面，一篇是如何这只默认Guest机器的默认Shell；另外一篇是问题解决，当我在Guest机器中将系统升级后，出现 `guest additions` 不匹配的问题。

JavaScript测试那篇文章，主要描述在编写JavaScript单元测试的时候会涉及到的框架，以及使用示例。

## Switch to your favorite shell in Vagrant

如果有使用Vagrant，并且想在Vagrant box里面使用zsh，这篇文章可以帮你轻松的解决这个问题。文章中举例通过Chef和Puppet来安装，以下代码是通过Chef来安装：


<!--more-->


    # Using Chef
    package 'zsh'
    
    execute "set zsh as default shell" do
      command "chsh -s $(which zsh) vagrant"
    end

## Vagrant Tip: Sync VirtualBox Guest Additions

在将一个Vagrant box从Ubuntu 12.04升级到14.04后，`Reload`后提示：

    The guest additions on this VM do not match the install version of VirtualBox!

文章通过介绍通过安装一个`Vagrant Plugin`来解决这个问题，简单方便。

    Testing in Browsers and Node with Mocha, Chai, Sinon, and Testem

写JavaScript测试时，你可能使用到的框架以及框架的使用例子。非常实用。

## TDD短迭代

编写一个测试（明确将要做什么）
运行测试，观察测试失败（确认测试代码以及测试用例）
使测试通过（放手去做，不要害怕硬编码）
重构（消除冗余，寻找更优方案）