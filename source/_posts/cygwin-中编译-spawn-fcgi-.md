---
title: "cygwin 中编译 spawn-fcgi "
categories: [ "Linux" ]
tags: [ "cygwin","spawn-fcgi" ]
draft: false
slug: "cygwin-compiler-spawnfcgi"
date: "2013-11-09 09:56:00"
---

spawn-fcgi是一个通用的FastCGI管理服务器

她是lighttpd中的一部份，但目前已经单独成为一个项目，最新的lighttpd没有这一块，但可以在以前版本中找到她

在lighttpd-1.4.15（ [http://www.lighttpd.net/download/lighttpd-1.4.15.tar.gz](http://www.lighttpd.net/download/lighttpd-1.4.15.tar.gz) ）中就有她


<!--more-->


Note

注：最新的spawn-fcgi可以到lighttpd.net网站搜索“spawn-fcgi”找到她的最新版本发布地址

目前她的下载地址是http://redmine.lighttpd.net/news/2 最新版本是http://www.lighttpd.net/download/spawn-fcgi-1.6.0.tar.gz  【2.新版本之后并不支持编译，反正我的报错。。。。。】

    tar -zxvf lighttpd-1.4.15.tar.gz
    cd lighttpd-1.4.15
    ./configure #编译
    make  #因为我不需要安装lighttp而是只需要他其中的某个文件，所以只make就可以了，不需要make install
    cp src/spawn-fcgi /usr/local/bin/spawn-fcgi  #取出spawn-fcgi的程序


下面我们就可以使用 spawn-fcgi 来控制php-cgi的FastCGI进程了

`/usr/local/bin/spawn-fcgi -a 127.0.0.1 -p 9000 -C 5 -u www-data -g www-data -f /usr/bin/php-cgi`


参数含义如下

 

    -f <fcgiapp> 指定调用FastCGI的进程的执行程序位置，根据系统上所装的PHP的情况具体设置
     -a <addr> 绑定到地址addr
     -p <port> 绑定到端口port
     -s <path> 绑定到unix socket的路径path
     -C <childs> 指定产生的FastCGI的进程数，默认为5（仅用于PHP）
     -P <path> 指定产生的进程的PID文件路径
     -u和-g FastCGI使用什么身份（-u 用户 -g 用户组）运行，Ubuntu下可以使用www-data，其他的根据情况配置，如nobody、apache等