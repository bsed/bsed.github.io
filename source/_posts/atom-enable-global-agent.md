---
title: atom 启用全局代理更新插件和主题
date: 2016-09-18 21:20:00
updated: 2016-09-22 15:23:56
tags: 
- linux
- dns
categories: 
- linux

---
全局文件 是 `.apmrc`

**windows** .apmrc 文件 在 `%USERPROFILE%\.atom\.app\.apmrc`.
**ubuntu** .apmrc 文件 在 `~\.atom\.apmrc`
mac 系统找到 .apmrc 修改即可。

需要**注意**的时，当atom 启动的时候， `.apmrc` 文件将被重写，所以， 先打开atom后，再修改 `.apmrc`文件内容。
下次启动的时候需要重新修改 .apmrc.


<!--more-->


把下面的端口 改成自己的即可
```
http-proxy = "http://localhost:8123"
https-proxy = "http://localhost:8123"
proxy = "http://localhost:8123"
```
