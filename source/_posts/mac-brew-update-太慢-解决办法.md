---
title: "mac brew update 太慢 解决办法"
categories: [ "系统" ]
tags: [ "mac","brew" ]
draft: false
slug: "solution-to-slow-mac-brew-update"
date: "2020-05-02 18:09:00"
---

使用Homebrew安装软件的时候一直卡在Update阶段。同时发现从github.com下载文件也极度缓慢（几十 KB/s）。
主要是资源访问太慢的原因造成的，我们这里需要替换一下镜像。terminal 只有顶部的title栏会变化，最后才会出现更新结果

## 使用中科大的镜像

## 替换默认源


<!--more-->


### （一）替换brew.git
```bash
cd "$(brew --repo)"
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
```
###（二）替换homebrew-core.git
```
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
```
###（三）替换homebrew-bottles:

bash 用户：
```bash
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
```
zsh 用户：
```bash
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
```

最后使用使用下面命令观察update过程：

```bash
brew update --verbose
```
进行更新，发现速度变的很快。替换镜像完成。
