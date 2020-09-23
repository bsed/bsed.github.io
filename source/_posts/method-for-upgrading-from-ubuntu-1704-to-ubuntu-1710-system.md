---
title: 从Ubuntu 17.04升级到Ubuntu 17.10系统的方法
date: 2017-05-06 13:12:00
updated: 2017-05-06 13:17:34
tags: 
- ab
- locust
categories: 
- linux

---
采用以下方法把你的Ubuntu 17.04系统升级到Ubuntu 17.10系统[开发版本]，以下是升级方式：
 
先更新资源链接
```bash
$sudo apt-get update
```
升级版本，一路确定就可以了。[中国用户需要翻墙]
 
重新启动后再次使用此命令才可以升级版本。
```
$sudo update-manager  -c  -d
```
查看版本，如图所示：

![ubuntu17.10.png][1]


  [1]: https://imgs.gnux.cn/usr/uploads/2017/05/1155862573.png