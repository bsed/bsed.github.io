---
title: 在Mac OS X上移除JDK
date: 2020-05-08 12:53:43
updated: 2020-05-08 12:53:43
tags: 
- pyenv
categories: 
- default

---
首先你得知道你的电脑中安装了哪些jdk（mac可以安装多个jdk）
打开mac的终端，输入命令：
```bash
ls /Library/Java/JavaVirtualMachines/
```
即可查看你电脑中装了哪些jdk版本。

然后就可以看到你电脑上安装的jdk版本：


<!--more-->


我的是jdk1.8.0_251版本的。

最后卸载的时候输入命令即可卸载：
```bash
sudo rm -rf /Library/Java/JavaVirtualMachines/jdk1.8.0_251.jdk
```
卸载jdk
