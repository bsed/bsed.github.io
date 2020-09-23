---
title: Github: error cloning my private repository
date: 2018-07-24 11:17:00
updated: 2018-07-24 11:28:49
tags: 
- javascript
- vue
- render
categories: 
- js

---
笔者在dep init golang 项目时，报如下错误：
```bash
E:\gocode\src\maotai>dep init
Importing configuration from godep. These are only initial constraints, and are further refined during the solve process.
Detected godep configuration files...
Converting from Godeps.json ...
  Using ^0.2.0 as initial constraint for imported dep github.com/bsed/captcha
  Trying v0.2 (d28fba5) as initial lock for imported dep github.com/bsed/captcha
  Using master as initial constraint for imported dep github.com/bsed/graphics-go
  Trying master (9176ab3) as initial lock for imported dep github.com/bsed/graphics-go
  Using master as initial constraint for imported dep github.com/ccpaging/go-colortext
  Trying master (8439ff4) as initial lock for imported dep github.com/ccpaging/go-colortext
  Trying * (f5ebb23) as initial lock for imported dep github.com/ccpaging/log4go
  Warning: Skipping project. Unable to import lock "dbeaa9332f19a944acb5736b4456cfcc02140e29" for github.com/dgrijalva/jwt-go: unable to list versions for github.com/dgrijalva/jwt-go(): failed to list versions for https://github.com/dgrijalva/jwt-go: fatal: unable to access 'https://github.com/dgrijalva/jwt-go/': error setting certificate verify locations:
  CAfile: /mingw64/ssl/certs/ca-bundle.crt
  CApath: none
: exit status 128
  Trying * (d6884c7) as initial lock for imported dep github.com/didip/tollbooth
  Using ^1.30.3 as initial constraint for imported dep github.com/go-ini/ini
  Trying v1.30.3 (f280b3b) as initial lock for imported dep github.com/go-ini/ini
  Using ^0.11.2 as initial constraint for imported dep github.com/go-playground/locales
  Trying v0.11.2 (e4cbcb5) as initial lock for imported dep github.com/go-playground/locales
  Warning: Skipping project. Unable to import lock "71201497bace774495daed26a3874fd339e0b538" for github.com/go-playground/universal-translator: unable to list versions for github.com/go-playground/universal-translator(): failed to list versions for https://github.com/go-playground/universal-translator: fatal: unable to access 'https://github.com/go-playground/universal-translator/': error setting certificate verify locations:
  CAfile: /mingw64/ssl/certs/ca-bundle.crt
  CApath: none
: exit status 128
  Trying * (03c0c1b) as initial lock for imported dep github.com/go-redis/redis
  Warning: Skipping project. Unable to import lock "b28538b2e3f5446fe6e0e47ee34cd74b662ca2ab" for github.com/labstack/echo: unable to list versions for github.com/labstack/echo(): failed to list versions for https://github.com/labstack/echo: fatal: unable to access 'https://github.com/labstack/echo/': error setting certificate verify locations:
  CAfile: /mingw64/ssl/certs/ca-bundle.crt
  CApath: none
: exit status 128
  Warning: Skipping project. Unable to import lock "57409ada9da0f2afad6664c49502f8c50fbd8476" for github.com/labstack/gommon: unable to list versions for github.com/labstack/gommon(): failed to list versions for https://github.com/labstack/gommon: fatal: unable to access 'https://github.com/labstack/gommon/': error setting certificate verify locations:
  CAfile: /mingw64/ssl/certs/ca-bundle.crt
  CApath: none
: exit status 128
  Warning: Skipping project. Unable to import lock "ad5389df28cdac544c99bd7b9161a0b5b6ca9d1b" for github.com/mattn/go-colorable: unable to list versions for github.com/mattn/go-colorable(): failed to list versions for https://github.com/mattn/go-colorable: fatal: unable to access 'https://github.com/mattn/go-colorable/': error setting certificate verify locations:
  CAfile: /mingw64/ssl/certs/ca-bundle.crt
  CApath: none
: exit status 128
  Warning: Skipping project. Unable to import lock "a5cdd64afdee435007ee3e9f6ed4684af949d568" for github.com/mattn/go-isatty: unable to list versions for github.com/mattn/go-isatty(): failed to list versions for https://github.com/mattn/go-isatty: fatal: unable to access 'https://github.com/mattn/go-isatty/': error setting certificate verify locations:
  CAfile: /mingw64/ssl/certs/ca-bundle.crt
  CApath: none
: exit status 128
  Trying * (891127d) as initial lock for imported dep github.com/nfnt/resize
  Trying * (6bb2691) as initial lock for imported dep github.com/oliamb/cutter
  Using ^2.1.0 as initial constraint for imported dep github.com/patrickmn/go-cache
  Trying v2.1.0 (a3647f8) as initial lock for imported dep github.com/patrickmn/go-cache
  Using ^1.1.0 as initial constraint for imported dep github.com/pborman/uuid
  Trying v1.1 (e790cca) as initial lock for imported dep github.com/pborman/uuid
  Warning: Skipping project. Unable to import lock "e746df99fe4a3986f4d4f79e13c1e0117ce9c2f7" for github.com/valyala/bytebufferpool: unable to list versions for github.com/valyala/bytebufferpool(): failed to list versions for https://github.com/valyala/bytebufferpool: fatal: unable to access 'https://github.com/valyala/bytebufferpool/': error setting certificate verify locations:
  CAfile: /mingw64/ssl/certs/ca-bundle.crt
```
**解决办法**：


<!--more-->


 为了解决上面的错误，需要从下面链接下载GIT: [https://git-for-windows.github.io](https://git-for-windows.github.io) 秘钥文件路径如下:
```bash
D:\Program Files\Git\mingw64\ssl\certs\ca-bundle.crt
```
配置GIT路径
```bash
git config --system http.sslcainfo "D:\Program Files\Git\mingw64\ssl\certs\ca-bundle.crt"
```
# 代理
临时设置Windows下代理：
在控制台执行如下命令，后面的的代理值根据你具体的代理进行设置
```bash
set http_proxy=http://127.0.0.1:7777/pac?t=201603231602138322
set https_proxy=https://127.0.0.1:7777/pac?t=201603231602138322
```

临时设置Linux下代理：
在控制台执行如下命令，后面的的代理值根据你具体的代理进行设置
```bash
http_proxy=http://127.0.0.1:7777/pac?t=201603231602138322
https_proxy=https://127.0.0.1:7777/pac?t=201603231602138322
``` 

此时，在控制台执行 go get 时即自动会通过代理。