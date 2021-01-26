---
title: "windows10、linux 命令行下配置代理"
categories: [ "Linux" ]
tags: [ "windows","proxy" ]
draft: false
slug: "windows-10-linux-configuration-agent"
date: "2019-05-14 17:03:00"
---

控制代理的环境变量分别是 http_proxy、http_proxy_user、http_proxy_pass，不区分大小写，分别代表代理地址（应是 http://ip:port 的形式）、代理用户名、代理密码，一般情况下只需要配置 http_proxy 即可


<!--more-->

```bash
http_proxy=http://localhost:1080
http_proxy_user=zhangsan
http_proxy_pass=lisi
通过 set 命令的形式大致如下所示。

#设置参数
set http_proxy=http://localhost:1080
set http_proxy_user=zhangsan
set http_proxy_pass=lisi

#删除参数
set http_proxy=
set http_proxy_user=
set http_proxy_pass=
另外经测试还有 https_proxy 环境变量可配置，用于配置 https 的代理，如果未配置则将使用 http_proxy 的配置。据此可推测有 https_proxy_user 等参数。
```
Linux
因目前没有环境测试，故以下结论仅根据网上资料整理并推测所得，仅做记录和供参考，详见参考资料。

据资料得，Linux 配置方式与 Windows 相似，仅命令及配置方式有所不同。

可配置的环境变量名分别为 http_proxy、https_proxy、ftp_proxy、no_proxy，分别是配置 http 代理、https 代理、ftp 代理、不使用代理的地址，参数格式大致如下所示（正确性有待考察，可能需要加 http:// 前缀），no_proxy 较特殊。
```bash
http_proxy=http://localhost:1080
https_proxy=http://localhost:1080
ftp_proxy=http://localhost:1080
no_proxy="127.0.0.1, localhost, 172.26.*, 172.25.6.66, 192.168.*"
```