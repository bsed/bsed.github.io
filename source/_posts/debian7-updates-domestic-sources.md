---
title: UCloud debian7 更新国内源
date: 2018-08-14 04:21:00
updated: 2018-08-14 15:41:56
tags: 
- linux
- bash
- shell
categories: 
- linux

---
刚在UCloud买了个云主机试试，发现 debian7 镜像源无法使用，这就需要切换镜像源了。
如图：
![mirror.ucloud.png][1]
debian7 更新国内源
```bash
vi /etc/apt/sources.list
```
`dd`命令删除整行


<!--more-->


**阿里云源**
```bash
deb http://security.debian.org/ wheezy/updates main
deb-src http://security.debian.org/ wheezy/updates main
deb http://mirrors.aliyun.com/debian wheezy main contrib non-free
deb-src http://mirrors.aliyun.com/debian wheezy main contrib non-free
deb http://mirrors.aliyun.com/debian wheezy-updates main contrib non-free
deb-src http://mirrors.aliyun.com/debian wheezy-updates main contrib non-free
deb http://mirrors.aliyun.com/debian-security wheezy/updates main contrib non-free
deb-src http://mirrors.aliyun.com/debian-security wheezy/updates main contrib non-free
```
**或者网易源**

```bash
deb http://security.debian.org/ wheezy/updates main
deb-src http://security.debian.org/ wheezy/updates main
deb http://mirrors.163.com/debian/ wheezy main non-free contrib
deb http://mirrors.163.com/debian/ wheezy-updates main non-free contrib
deb http://mirrors.163.com/debian/ wheezy-backports main non-free contrib
deb-src http://mirrors.163.com/debian/ wheezy main non-free contrib
deb-src http://mirrors.163.com/debian/ wheezy-updates main non-free contrib
deb-src http://mirrors.163.com/debian/ wheezy-backports main non-free contrib
deb http://mirrors.163.com/debian-security/ wheezy/updates main non-free contrib
deb-src http://mirrors.163.com/debian-security/ wheezy/updates main non-free contrib
```


  [1]: https://imgs.gnux.cn/usr/uploads/2018/08/3370658123.png