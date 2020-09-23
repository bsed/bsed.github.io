---
title: 使用logrotate分割tomcat日志
date: 2014-06-15 08:56:00
updated: 2015-11-07 09:01:12
tags: 
- javascript
- array
- obj
categories: 
- js

---
日志是Linux系统中最重要的部分之一，通过日志可以知道系统中正在发生什么或者发生过什么，极大的方便了系统管理，常见的linux发行版中都自带了logrotate程序来管理系统日志，当然logrotate也可以用来管理软件日志。使用logrotate有以下几点好处：

1.大多数系统自带，不用安装
2.配置简单，功能很强大
3.可靠性高

我生产上就是用Logrotate来管理tomcat日志，由于生产环境上tomcat比较忙碌，每天的日志都十几G，已经到了不得不分割的程度。配置方法：

首先要配置tomcat日志的分割，然后通过指定日志分割时间。

## 一、配置tomcat的日志分割

在`logrotate`配置目录 `/etc/logrotate.d/` 下新建一个名为tomcat的文件，内容为：

    /opt/tomcat-7.0/logs/catalina.out{
    rotate 2
    copytruncate
    nocompress
    notifempty
    missingok
    nomail
    noolddir 
    daily
    dateext
    }

这里参数的详细说明附在最后。

## 二、指定分割时间

由于logrotate是基于cronjob执行的，默认cronjob daily的定时任务执行时间是4：02分（centos 5系列），我们是要每天的日志转储成一个新文件，而不是两天的日志混在一起，所以还要修改一下cron daily的执行时间：
centos 5 系列`cronjob daily`执行时间是在`/etc/crontab`文件中配置。

`59 23 * * * root run-parts /etc/cron.daily`

注意时间是在每天23：59分执行，这里转储后的文件名中的日期才是正确的.

centos 6 系列daily、monthly等文件夹里面的任务已经不用crond处理，而是交给anacron处理，执行时间有延迟时间不是固定的，要自定义这些定时任务的执行时间有三种方法：

### 1.关闭anacron,使用crond管理daily等任务的时间

    yum remove cronie-anacron
    yum install cronie-noanacron

之后加上：

    01 * * * * root run-parts /etc/cron.hourly
    59 23 * * * root run-parts /etc/cron.daily
    22 4 * * 0 root run-parts /etc/cron.weekly
    42 4 1 * * root run-parts /etc/cron.monthly

这样就和centos 5系列一样，可以通过修改/etc/crontab来控制定时任务执行时间了

### 2.编辑/etc/anacrontab文件

首先要设置RANDOM_DELAY=0和START_HOURS_RANGE=0-0，这样可以设置为每天零点执行，在将cron.daily设置成

    1       0      cron.daily              nice run-parts /etc/cron.daily

看了一下anacron说明和anacrontab文件的格式，觉得这种方法可行，但是我没有测试，如果想用这种方法，请务必先测试。

### 3.将tomcat 文件放在其它目录，这样每天执行cron.daily时就不会分割，然后添加定时任务来分割

    59 23 * * * root run-parts /usr/sbin/logrotate /root/tomcat

## 三、logrotate参数说明

    参数名称    含义
    compress                 通过gzip 压缩转储以后的日志
    nocompress               不压缩转储
    copytruncate             打开中的日志转储
    copytruncate            打开中的日志转储
    nocopytruncate              备份日志文件，但是不截断
    nocreate                    不建立新日志
    delaycompress           延迟压缩，和compress一起使用时，转储的日志文件到下一次转储时才压缩
    nodelaycompress         转储并压缩
    errors address          转储时错误信息发送指定地址
    ifempty                 文件为空也转储
    notifempty                  如果文件为空，不转储
    mail address            发送转储日志到指定邮箱
    nomail                  转储不发邮件
    olddir directory            转储后的日志文件放入指定的目录，必须和当前日志文件在同一个文件系统
    noolddir                    转储后的日志文件和当前日志文件放在同一个目录下
    prerotate/endscript         在转储以前需要执行的命令可以放入这个对，这两个关键字必须单独成行
    postrotate/endscript    在转储以后需要执行的命令可以放入这个对，这两个关键字必须单独成行
    daily                   按天存储
    weekly                  按周存储
    monthly                 按月存储
    rotate count            日志转存保留的前多少份，多余的会被删除
    tabootext [+] list      让logrotate 不转储指定扩展名的文件，缺省的扩展名是：.rpm-orig, .rpmsave, v, 和 ~
    size size           当日志文件到达指定的大小时才转储，Size 可以指定 bytes (缺省)以及KB (sizek)或者MB
    extension           指定转存日志后缀名，例如 .log
    dateformat          设置日志文件名日期格式默认为 %Y%m%d

参考文章：

[http://blog.jikr.cn/logrotate-cut-the-web-logs/](http://blog.jikr.cn/logrotate-cut-the-web-logs/)
[http://abdussamad.com/archives/390-Scheduling-system-maintenance-cron-jobs-in-CentOSRHEL-6.x.html](http://abdussamad.com/archives/390-Scheduling-system-maintenance-cron-jobs-in-CentOSRHEL-6.x.html)
