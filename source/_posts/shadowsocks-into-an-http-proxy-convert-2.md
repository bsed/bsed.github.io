---
title: Convert Shadowsocks into an HTTP proxy
date: 2013-12-04 10:40:00
updated: 2016-12-04 10:42:06
tags: 
- javascript
- async
categories: 
- js

---
First run polipo with parent proxy set to Shadowsocks:
```bash
apt-get install polipo
service polipo stop
polipo socksParentProxy=localhost:1080
```
Then you can play with the HTTP proxy:
```bash
http_proxy=http://localhost:8123 apt-get update

http_proxy=http://localhost:8123 curl www.google.com

http_proxy=http://localhost:8123 wget www.google.com
```


<!--more-->

## git下使用 proxy

```bash
git config --global http.proxy 127.0.0.1:8123
git clone https://github.com/xxx/xxx.git
git xxx
git xxx
git config --global --unset-all http.proxy
```
https://github.com/shadowsocks/shadowsocks/wiki/Convert-Shadowsocks-into-an-HTTP-proxy