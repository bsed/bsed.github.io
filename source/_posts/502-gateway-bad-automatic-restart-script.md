---
title: 502 Bad Gateway 自动重启脚本
date: 2013-11-26 21:48:00
updated: 2016-11-21 21:49:46
tags: 
- hadoop
- sqoop
categories: 
- bigdata

---
一个简单的脚本，用来监控502的，如果遇到502，自动重启PHP进程。适用于LNMP环境。

以下文件，存放于 `/root` 或其它目录：
```
#!/bin/bash
MY_URL="http://kelvin.mbioq.com/"
RESULT=`curl -I $MY_URL|grep "HTTP/1.1 502"`
if [ -n "$RESULT" ]; then
        /etc/init.d/php-fpm restart
fi
```


<!--more-->


给予可执行权限：
```
chmod +x /root/php.sh
```
通过 crontab 设置自动运行：

crontab -e
* * * * * /root/php.sh