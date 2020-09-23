---
title: 解决 libcurl.so.4: no version information available
date: 2017-05-17 22:18:00
updated: 2017-05-17 22:18:26
tags: 
- vscode
- rust
categories: 
- default

---
使用自编译的 curl 后，可能会遇到这个问题：
```bash
/usr/bin/curl: /usr/local/lib/libcurl.so.4: no version information available (required by /usr/bin/curl)
```
冲突了，很坑。


<!--more-->


## 解决方案

1. 首先定位一下 libcurl 的位置：
```
locale libcurl.so.4
```
大概是这样：
```
/usr/lib/x86_64-linux-gnu/libcurl.so.4
/usr/lib/x86_64-linux-gnu/libcurl.so.4.3.0
/usr/local/lib/libcurl.so.4
/usr/local/lib/libcurl.so.4.4.0
```
2.将这个冲突的软链接删掉：
```bash
rm -rf /usr/local/lib/libcurl.so.4
```
3. 然后，将 4.3.0 的静态库链接到上面：
```bash
ln -s /usr/lib/x86_64-linux-gnu/libcurl.so.4.3.0 /usr/local/lib/libcurl.so.4
```
验证一下：
```bash
ls -l /usr/local/lib/libcurl.so.4
```
应该返回如下：
```bash
lrwxrwxrwx 1 root root 42 Apr 3 20:35 /usr/local/lib/libcurl.so.4 -> /usr/lib/x86_64-linux-gnu/libcurl.so.4.3.0
```
OK，搞定了。

参考：http://stackoverflow.com/questions/30017397/error-curl-usr-local-lib-libcurl-so-4-no-version-information-available-requ
