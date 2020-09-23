---
title: Liunx下删除7天前大日志sh脚本
date: 2015-10-25 08:21:00
updated: 2015-10-25 08:21:57
tags: 
- vim
- split
categories: 
- vim

---
> 这个脚本可以把它放在crontab里面每天自动执行

 Linux得到七天前的日期脚本

`oldday=`date +%Y-%m-%d -d "-7 day"``

**bsh代码：**


<!--more-->


    #进入到日志文件目录
    cd /home/web/tomcat-6.0.41/logs
    #pathlog执行这个脚本动作的日志
    pathlog=/home/web/autosh/clearbigfile.log
    #把执行时间记录下来
    echo "`date` start-----" >> $pathlog
    #oldday这个行可以换成上面说的动态得到七天前的日期语句
    oldday="2014-07-07"
    #删除七天前的日志
    rm -rf catalina.$oldday.log
    rm -rf host-manager.$oldday.log
    rm -rf localhost.$oldday.log
    rm -rf manager.$oldday.log
    #把执行完的时间记录下来
    echo "`date` end------" >> $pathlog