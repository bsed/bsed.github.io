---
title: "Log Rotation with logrotate"
categories: [ "Linux" ]
tags: [ "ubuntu","logratate" ]
draft: false
slug: "log-rotation-with-logrotate"
date: "2016-12-22 09:23:00"
---

Ubuntu 自带了 logrotate 程序，其他系统需要自己安装。

查看 `/etc/logrotate.d/nginx` 这个配置文件


<!--more-->


````bash
# 测试方法
# /usr/sbin/logrotate -f /etc/logrotate.d/nginx
/data/logs/*.log {
        # 每天 rotate
        daily
        # 忽略丢失的日志文件，处理下一个
        missingok
        # 日志最多被 rotate 的次数
        rotate 10
        compress
        delaycompress
        notifempty
        create 0640 root root
        # 当有多个日志匹配时，下面的脚本只执行一次，防止跑多次
        sharedscripts
        # nginx will re-open its logs in response to the USR1 signal
        postrotate
                [ -s /var/run/nginx.pid ] && kill -USR1 `cat /var/run/nginx.pid`
        endscript
}
```