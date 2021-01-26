---
title: "SWOOLE的热更新实现"
categories: [ "PHP" ]
tags: [ "SWOOLE" ]
draft: false
slug: "hot-update-swoole-implementation"
date: "2015-01-04 18:29:00"
---

使用swoole_http_server替代php-fpm后，由于php长驻内存,修改了代码不能实时调试，需要去手动去重启服务,很是不方便，参考了持续的思考的文章swoole之代码热更新实现之后,决定使用inotify来监控文件状态的改变，来给swoole发送reload信号,来实现swoole的热更新。


<!--more-->


如何安装`inotify`就不写了，安装之后可以建立一个脚本文件，

如`php_reload.sh:`

    #!/bin/sh
    # src 需要监控的地址
    src=/home/server/Project/test/app/
    /usr/bin/inotifywait -dmrq --timefmt '%d/%m/%y/%H:%M' --format '%T%w%f' -o /home/server/Project/test/bin/reload.log -e create $src | while read file
      do
            /home/server/Project/test/bin/httpserver reload
      done

配合上一篇的脚本使用非常方便,现在可以实时的修改脚本并查看结果了。
