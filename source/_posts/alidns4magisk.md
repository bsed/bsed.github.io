---
title: AliDNS4Magisk推荐
date: 2019-06-11 12:17:00
updated: 2019-06-11 12:17:27
tags: 
- mysql
- deadlock
categories: 
- sql

---
IPv4使用了由阿里云提供的`AliDNS`, 而IPv6则是由Cloudflare提供的`Cloudflare DNS`

如果不希望使用AliDNS，也可自行更改。

需要Magisk v16及以上


<!--more-->


如需修改DNS，解压后，修改以下相关字段即可。
```bash
config.sh
common/service.sh
system.prop
```

下载：[AliDNS4Magisk.zip][1]


  [1]: https://imgs.gnux.cn/usr/uploads/2019/06/3056609364.zip