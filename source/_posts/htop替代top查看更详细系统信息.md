---
title: "htop替代top查看更详细系统信息"
categories: [ "Linux" ]
tags: [ "linux","htop" ]
draft: false
slug: "htop-replaces-top-to-view-more-detailed-system-information"
date: "2016-11-07 21:20:00"
---

今天介绍大家一个比较高级一点的软件，名字叫htop，看到的内容更加的详细，安装如下：
![htop.png][1]
## htop
```bash
yum install htop #centos系统
apt-get install htop #debian或ubuntu系统
```
如果源里没有，可以自行编译安装：
```bash
wget http://ftp.gnu.org/pub/gnu/ncurses/ncurses-5.9.tar.gz
tar xvfz ncurses-5.9.tar.gz
cd ncurses-5.9
./configure
make
make install
wget http://sourceforge.net/projects/htop/files/htop/0.9/htop-0.9.tar.gz
tar zxvf htop-0.9.tar.gz
cd htop-0.9
./configure
make
make install
```


  [1]: https://imgs.gnux.cn/usr/uploads/2016/11/721382596.png