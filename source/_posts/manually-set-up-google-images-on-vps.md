---
title: 在VPS上手动搭建Google镜像
date: 2016-01-29 15:14:00
updated: 2016-01-31 15:16:38
tags: 
- css
- html
categories: 
- css

---
![yigrherb_sognuxcn.png][1]

用自己的VPS搭建Google反向代理​

## 一、准备工作

效果见: [http://so.gnux.cn](http://so.gnux.cn)
首先要感谢wen.lu的开源。参见GitHub地址：

[https://github.com/cuber/ngx_http_google_filter_module](https://github.com/cuber/ngx_http_google_filter_module)

一台VPS，要在墙外（美国、日本、香港等，）。本人的为Ubuntu14.04系统64位，因为目前网上流传的教程都是基于这个系统。下面开始教程。

## 最简安装


<!--more-->


### 安装 gcc & git#

```bash
​apt-get install build-essential git gcc g++ make
```

##​# 下载最新版源码# nginx

```bash
wget "http://nginx.org/download/nginx-1.7.8.tar.gz"
```
​
### 下载最新版 pcre pcre

```bash
wget "ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.38.tar.gz"
```

### 下载最新版 openssl

```bash
wget "https://www.openssl.org/source/openssl-1.0.1j.tar.gz"
```

### 下载最新版 zlib

```bash
wget "http://zlib.net/zlib-1.2.8.tar.gz"

### 下载ngx_http_google_filter_module本扩展

```bash
​git clone https://github.com/cuber/ngx_http_google_filter_module
```

### 下载 substitutions 扩展

```bash
​git clone https://github.com/yaoweibin/ngx_http_substitutions_filter_module
```

### 解压缩

```bash
​tar xzvf nginx-1.7.8.tar.gz
tar xzvf pcre-8.38.tar.gz
tar xzvf openssl-1.0.1j.tar.gz
tar xzvf zlib-1.2.8.tar.gz
```
### 进入 nginx 源码目录

```bash
​cd nginx-1.7.8
```
### 设置编译选项
```bash
 ./configure \
 --prefix=/opt/nginx-1.7.8 \
 --conf-path=/usr/local/nginx/nginx.conf \
  --with-pcre=../pcre-8.38 \
  --with-openssl=../openssl-1.0.1j \
  --with-zlib=../zlib-1.2.8 \
  --with-http_ssl_module \
  --add-module=../ngx_http_google_filter_module \
  --add-module=../ngx_http_substitutions_filter_module
```

[https://github.com/cuber/ngx_http_google_filter_module/blob/master/README.zh-CN.md](https://github.com/cuber/ngx_http_google_filter_module/blob/master/README.zh-CN.md)


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/2186059412.png