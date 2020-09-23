---
title: Ubuntu17 Swap 优化设置
date: 2017-03-01 09:05:00
updated: 2017-03-01 10:33:01
tags: 
- php
categories: 
- php

---
如果有使用VPS如 [Digital Ocean](https://m.do.co/c/e52118615626)、 [Linode ](https://www.linode.com/?r=d840eafca00d5510feda13c91cd17644aa037c35)常会遇到记忆体不够用的情形，除了砸钱升级外就是使用Swap，然而 进行Swap是很贵的行为，会拖慢系统的速度，透过调整下面介绍的几个参数 ，多少可以减轻一些负担

## swappiness
swappiness参数用以调整进行Swap的频率，值可以是0~100， 越低的值会降低系统进行Swap的频率，越接近100则系统会经常地将data 送进Swap让记忆体空间保持乾淨，实务上不要让系统太倚赖Swap才能产生较好的效能。


<!--more-->


透过以下指令可以得到目前swappiness的设定值
```bash
cat /proc/sys/vm/swappiness
```
预设值应为`60`，在一般电脑上不算太差，但在我们的VPS上会希望越接近0 越好，透过以下指令可以设定swapiness：
```bash
sudo sysctl vm.swappiness=10
```
这样的设定再重开机后就没用了，还要修改`sysctl.conf`档案：
```
sudo vim /etc/sysctl.conf
```
于档案底端加入以下设定后储存
```bash
vm.swappiness=10
```
## vfs_cache_pressure
`vfs_cache_pressure`控制系统记忆体重新快取目录与inode档案的程度， 经常快取的成本相当高，所以这个值不要太高比较好

透过以下指令得到目前系统的`vfs_cache_pressure`(预设应为100)：
```bash
cat /proc/sys/vm/vfs_cache_pressure
```
透过以下指令将vfs_cache_pressure降低：
```bash
sudo sysctl vm.vfs_cache_pressure=50
```
同样要修改`/etc/sysctl.conf`确保日后重开机仍以此设定值运行， 于档案底端加入以下设定后储存：
```bash
vm.vfs_cache_pressure=50
```

最后附上我的配置

```bash
vm.swappiness=1
vm.vfs_cache_pressure=50
vm.dirty_background_bytes=268435456
vm.dirty_bytes=1073741824
```
参考：

 - [高寫入系統 Linux
   效能調教](http://blog.littlero.se/post/linux-tuning-for-write-heavy-system/)
 - [Improved default settings for Linux
   machines](https://tobert.github.io/post/2014-06-24-linux-defaults.html)