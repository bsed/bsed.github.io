---
title: ERROR: The Python ssl extension was not compiled. Missing the OpenSSL lib?
date: 2019-08-25 07:43:00
updated: 2019-08-25 15:44:08
tags: 
- mirror
- archlinux
- archlabs
- manjaro
categories: 
- linux

---
我想在我的新ArchLinux安装上通过pyenv安装python

pyenv install 3.5.1Downloading Python-3.7.4.tar.xz...
-> https://www.python.org/ftp/python/3.7.4/Python-3.7.4.tar.xz
Installing Python-3.7.4...
ERROR: The Python ssl extension was not compiled. Missing the OpenSSL lib?


<!--more-->


Please consult to the Wiki page to fix the problem.
https://github.com/pyenv/pyenv/wiki/Common-build-problems


由于缺少OpenSSL支持, 提示失败。
```bash
sudo pacman -S --needed base-devel openssl zlib bzip2 readline sqlite curl \
llvm ncurses xz tk libffi python-pyopenssl git

sudo pacman -S ncurses5-compat-libs

sudo pacman -S  base-devel openssl zlib 

sudo pacman -S python2-pyopenssl

sudo pacman -S libssl-dev

CFLAGS=-I/usr/include/openssl-1.0 \
LDFLAGS=-L/usr/lib64/openssl-1.0 \
pyenv install -v 3.7.4
```
解决方案：
```
╰─pacman -Q | grep openssl
openssl 1.1.1.c-1
openssl-1.0 1.0.2.s-1
python-pyopenssl 19.0.0-1
python2-pyopenssl 19.0.0-1
```

- [pyenv](https://github.com/pyenv/pyenv/wiki/common-build-problems)