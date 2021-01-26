---
title: "解决 vscode 在 ubuntu 下 debug c++  找不到 glibc 的问题"
categories: [ "Linux" ]
tags: [ "c++","debug","rust" ]
draft: false
slug: "solve-the-problem-that-vscode-cant-find-glibc-in-debug-c-under-ubuntu"
date: "2019-05-01 09:33:00"
---

在进行调试单步进`mem::transmute::<*const libc::c_void, F>(__cxa_thread_atexit_impl)
            (dtor, t, &__dso_handle as *const _ as *mut _);`的时候老是有这个错误 无法打开“cxa_thread_atexit_impl.c”: 找不到文件(/build/glibc-KRRWSm/glibc-2.29/stdlib/cxa_thread_atexit_impl.c


<!--more-->


英文：
Unable to open 'cxa_thread_atexit_impl.c': File not found (file:///build/glibc-KRRWSm/glibc-2.29/stdlib/cxa_thread_atexit_impl.c).
如图：
![vscode_ubuntu_debug_rust.png][1]
**解决办法：**

可以在根目录下面新建文件夹/build/glibc-KRRWSm，然后把glibc从网上下载并解压就可以。
```bash
cd /
sudo mkdir build
cd build
sudo mkdir glibc-KRRWSm
cd glibc-KRRWSm
sudo wget http://ftp.gnu.org/gnu/glibc/glibc-2.29.tar.gz
sudo tar –zxvf glibc-2.29.tar.gz
```


  [1]: https://imgs.gnux.cn/usr/uploads/2019/05/2352686346.png