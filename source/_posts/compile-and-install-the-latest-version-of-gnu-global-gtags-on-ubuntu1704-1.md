---
title: 在ubuntu17.04 上编译安裝最新版 gnu global (gtags) 
date: 2017-04-01 11:40:00
updated: 2017-04-04 11:40:40
tags: 
- 俞敏洪
categories: 
- default

---
[官方](http://www.gnu.org/software/global/download.html)已经发布到了 [6.5.6](http://tamacom.com/global/global-6.5.6.tar.gz) 版了.

## 方法
安装编译所依赖的包：


<!--more-->


```bash
sudo apt-get build-dep global
sudo apt-get install libncurses5-dev libncursesw5-dev
```
从gnu global 官方下載[最新的]((http://tamacom.com/global/global-6.5.6.tar.gz)) tar 包并解压。
```
./configure --with-sqlite3
make -j4
make check
sudo make install
sudo make installcheck
```
Ok,大功告成。
可用 `global --version` 检查一下版本。

成功提示如下：
```bash
kelvin@gnux:~/tools/global-6.5.6> global --version             
global (GNU GLOBAL) 6.5.6
Copyright (c) 1996-2016 Tama Communications Corporation
License GPLv3+: GNU GPL version 3 or later <http://www.gnu.org/licenses/gpl.html>
This is free software; you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
```
```