---
title: "在Ubuntu15.04 上安装 oh-my-zsh"
categories: [ "Linux" ]
tags: [ "ubuntu","zsh" ]
draft: false
slug: "install-ohmyzsh-on-ubuntu1504"
date: "2015-04-13 22:29:00"
---

## 依赖软件（Dependencies）

`oh-my-zsh` 的安装依赖于以下两个软件：`git`、`zsh`。

这两个软件都可以通过软件包来安装：

    sudo apt-get update
    sudo apt-get install git
    sudo apt-get install zsh

安装完之后，可以在Bash 里面输入 zsh，来创建一个新的Shell，并体验 zsh。

## 手动安装


<!--more-->


### 克隆代码库(Clone the repository)

    git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh

最后面的~/.oh-my-zsh 可以更改为你的心水目录的路径。

### 备份已存在的 ~/.zshrc 文件【可选操作】

    cp ~/.zshrc ~/.zshrc.bak

这里的~/.zshrc 文件是 zsh shell 的原始配置文件。

###  使用 oh-my-zsh 的配置文件

通过复制 oh-my-zsh 的配置文件模板，来创建新的Zsh 配置文件：

    cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

###  使用 Zsh 为默认的 shell

    chsh -s /bin/zsh

  秘密告诉你吧。chsh -s其实修改的就是/etc/passwd文件里和你的用户名相对应的那一行。现在我来查看下：

    From: 《[用chsh选择shell](http://roclinux.cn/?p=739)》

### 重启系统，完成配置

    sudo shutdown -r now

