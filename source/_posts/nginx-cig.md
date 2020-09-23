---
title: 让nginx支持cgi
date: 2014-12-16 13:03:00
updated: 2014-12-16 13:13:25
tags: 
- php
- imagettftext
categories: 
- php

---
英文原文：[http://wiki.nginx.org/Fcgiwrap](http://wiki.nginx.org/Fcgiwrap)

本文是关于如何在nginx上使用CGI的简单教程。

本文引用自：[http://nginx.localdomain.pl/wiki/FcgiWrap](http://nginx.localdomain.pl/wiki/FcgiWrap)

网上也有一些人的教程，但是他们的方法会可能比较麻烦，我的目标是引导你使用一个清晰的方法。


<!--more-->


## 在 Debian 或 Ubuntu 中安装  

在 Debian 和 Ubuntu 中已经有可用的包。简单输入

    aptitude install fcgiwrap

这时，你可以看一下项目文档说明，文件在`/usr/share/doc/fcgiwrap/README.Debian`。示例的配置文件在`/usr/share/doc/fcgiwrap/examples/nginx.conf`。

接下来的要做的就是创建一个配置文件副本到nginx配置目录（这样不会在fcgiwrap更新时被覆盖）。

    cp /usr/share/doc/fcgiwrap/examples/nginx.conf /etc/nginx/fcgiwrap.conf

在需要使用CGI是，在nginx配置文件的"server"节点增加以下代码

    # fast cgi support
    include /etc/nginx/fcgiwrap.conf;

这个deb包包含了一个改进过的init脚本(/etc/init.d/fcgiwrap),和下面的给出的是一样的。安装完包后，可以看一下 HttpFcgiModule 文档和 FcgiExample 示例。

##  手动安装  ##

第一步是安装前的准备工作。

如果是在apt包管理的系统下可以使用

    aptitude install git-core build-essential libfcgi-dev autoconf libtool automake

获取源码：

    cd /usr/local/src/
    git clone git://github.com/gnosek/fcgiwrap.git

编译源码：

    cd /usr/local/src/fcgiwrap
    autoreconf -i
    ./configure
    make
    mv fcgiwrap /usr/local/bin/

运行脚本:

    /etc/init.d/fcgiwrap

可以参考在Debian下面的

    #!/usr/bin/perl
    
    use strict;
    use warnings FATAL => qw( all );
    
    use IO::Socket::UNIX;
    
    my $bin_path = '/usr/local/bin/fcgiwrap';
    my $socket_path = $ARGV[0] || '/tmp/cgi.sock';
    my $num_children = $ARGV[1] || 1;
    
    close STDIN;
    
    unlink $socket_path;
    my $socket = IO::Socket::UNIX->new(
        Local => $socket_path,
        Listen => 100,
    );
    
    die "Cannot create socket at $socket_path: $!\n" unless $socket;
    
    for (1 .. $num_children) {
        my $pid = fork;
        die "Cannot fork: $!" unless defined $pid;
        next if $pid;
    
        exec $bin_path;
        die "Failed to exec $bin_path: $!\n";
    }

别忘了修改文件权限chmod +x /etc/init.d/fcgiwrap。

我决定不用复杂的初始化运行脚本，而用简单的方式，我只要在/etc/rc.local的exit 0行前加入以下代码

    sudo -u www-data /etc/init.d/fcgiwrap

这样我们的cgi就是使用www-data用户运行。

##  发生了什么  ##

这个脚本会使用www-data用户启动fcgiwrapper的初始化脚本，这个脚本会起一个监听进程绑定到/tmp/cgi.sock，这个就是你在fastcgi_pass需要使用到的fastcgi_pass unix:/tmp/cgi.sock;