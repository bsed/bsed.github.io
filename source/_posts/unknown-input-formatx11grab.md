---
title: with --enable-x11grab compile, Unknown input format: 'x11grab
date: 2019-07-06 22:09:00
updated: 2019-07-07 11:07:49
tags: 
- firefox
- gpu
categories: 
- linux

---
with `--enable-x11grab` and the configure passed without any errors, but x11 input wasn't enabled (I still had the message "Unknown input format: 'x11grab'

解决:
将 `--enable-x11grab` 修改为： `--enable-libxcb`