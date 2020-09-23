---
title: (solved) The pkg-config package 'libpcre' is required but it could not be found.
date: 2019-01-14 07:51:00
updated: 2019-01-14 08:41:06
tags: 
- mysql
categories: 
- sql

---
pkg-confg 确认是否存在。
```bash
$ pkg-config --libs libpcre
Package libpcre was not found in the pkg-config search path.
Perhaps you should add the directory containing `libpcre.pc'
to the PKG_CONFIG_PATH environment variable
No package 'libpcre' found
```
编辑profile


<!--more-->


```bash
$ vim ~/.bash_profile  
```
`.bash_profile`添加以下。

```bash
export PKG_CONFIG_PATH=/usr/lib/pkgconfig
```
保存
```bash
. ~/.bash_profile中 
```
验证
```bash
$ pkg-config --libs libpcre
-lpcre  
```