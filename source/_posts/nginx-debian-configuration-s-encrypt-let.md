---
title: Debian+Nginx配置Let’s Encrypt
date: 2015-11-23 11:41:00
updated: 2016-12-03 11:43:51
tags: 
- linux
- debian
- bash
categories: 
- linux

---
## debian安装Let’s Encrypt
```bash
apt update
apt install letsencrypt
letsencrypt --duplicate certonly --standalone --email peaksec@gmail.com -d kelvin.mbioq.com -d www.mbioq.com -d db.mbioq.com
```
## nginx需要的证书位置


<!--more-->


```bash
#certificate
/etc/letsencrypt/live/kelvin.mbioq.com/fullchain.pem
#privatekey
/etc/letsencrypt/live/kelvin.mbioq.com/privkey.pem
```
## nginx配置示例

```bash
listen 443;
...
ssl on;
ssl_certificate /etc/letsencrypt/live/kelvin.mbioq.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/kelvin.mbioq.com/privkey.pem;
ssl_session_timeout 10m;
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_prefer_server_ciphers on;
ssl_ciphers ALL:!aNULL:!ADH:!eNULL:!LOW:!EXP:RC4+RSA:+HIGH:+MEDIUM;
ssl_session_cache builtin:1000 shared:SSL:10m;
...
```