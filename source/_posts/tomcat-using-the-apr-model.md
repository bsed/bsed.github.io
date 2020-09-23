---
title:  tomcat使用apr模式
date: 2013-12-23 09:26:00
updated: 2015-11-07 09:27:24
tags: 
- javascript
- es5
categories: 
- js

---
优化tomcat时都会更改tomcat的运行模式，tomcat支持三种模式：BIO、NIO、APR。默认的BIO性能最差；NIO模式是内置的，使用的是java的异步io，性能强于bio，并且简单改一下配置即可使用；APR模式是使用JNI(Java Native Interface)提升静态文件和SSL的效率，是三种模式中性能最强的，也是配置最“复杂”的。这三种模式的原理和性能这里就不多说了，tomcat官网和网上都有很多说明和测评，这里记录一下APR的安装过程

需要一下三个库：

    APR library
    JNI wrappers for APR used by Tomcat (libtcnative)
    OpenSSL libraries

## 1.下载：

apr相关组件下载地址：[https://apr.apache.org/download.cgi](https://apr.apache.org/download.cgi)
wget [http://psg.mtu.edu/pub/apache//apr/apr-1.5.1.tar.gz](http://psg.mtu.edu/pub/apache//apr/apr-1.5.1.tar.gz)
wget [http://psg.mtu.edu/pub/apache//apr/apr-util-1.5.3.tar.gz](http://psg.mtu.edu/pub/apache//apr/apr-util-1.5.3.tar.gz)
libtcnative 这个不用下载，在tomcat的bin目录下有；openssl可以用系统的包管理安装，不用专门下载；有些特殊情况下还要编译apr-iconv这个包。


<!--more-->


## 2.编译安装

a.用系统的包管理安装openssl组件，centos系统使用：

    yum install openssl openssl-devel

debian系列使用：

    apt-get install openssl openssl-dev

b.开始编译,先编译apr再编译apr-util，使用经典三步来编译：
apr :

    ./configure && make && make install

在编译apr-util时要指定apr的路径

    configure  --with-apr=/usr/local/apr/ && make && make install

c.编译libtcnative,解压tomcat的bin目录下的tomcat-native.tar

    ./configure  --with-apr=/usr/local/apr/ --with-java-home=/opt/JDK1.6/ && make && make install

## 3.配置tomcat使用apr

a.在tomcat的bin目录下的catalina.sh中添加

    CATALINA_OPTS="$CATALINA_OPTS -Djava.library.path=/usr/local/apr/lib"

b.修改server.xml文件
修改虚拟主机的protocol为： protocol="org.apache.coyote.http11.Http11AprProtocol"
上面这些步骤做完之后就可以启动tomcat了，如果在catalina.sh中没有异常且有类似下面的日志就说明配置成功了

    Sun 17, 2014 12:29:06 PM org.apache.catalina.core.AprLifecycleListener init
    INFO: Loaded APR based Apache Tomcat Native library 1.1.29 using APR version 1.5.0.
    Jun 17, 2014 12:29:06 PM org.apache.catalina.core.AprLifecycleListener init
    INFO: APR capabilities: IPv6 [true], sendfile [true], accept filters [false], random [true].
    Jun 17, 2014 12:29:07 PM org.apache.catalina.core.AprLifecycleListener initializeSSL
    INFO: OpenSSL successfully initialized (OpenSSL 1.0.0-fips 29 Mar 2010)
    Jun 17, 2014 12:29:07 PM org.apache.coyote.AbstractProtocol init
    INFO: Initializing ProtocolHandler ["http-apr-8080"]
    Jun 17, 2014 12:29:07 PM org.apache.coyote.AbstractProtocol init
    INFO: Initializing ProtocolHandler ["ajp-apr-8009"]
    Jun 17, 2014 12:29:07 PM org.apache.catalina.startup.Catalina load
    INFO: Initialization processed in 653 ms

*注意：*

1.tomcat模式改为apr之后，配置https时不在是keystore+password的方式，而是和apache一样的crt+key方式了。
2.如果觉得apr模式麻烦，可以使用NIO模式，直接将protocol改为： protocol="org.apache.coyote.http11.Http11NioProtocol"即可，不需要编译任何东西。为追求性能只靠更改tomcat运行模式是不够的，使用apache+tomcat、nginx+tomcat搞横向集群或者纵向集群才是靠谱的方案。

参考文章：
[http://tomcat.apache.org/tomcat-6.0-doc/apr.html#Linux](http://tomcat.apache.org/tomcat-6.0-doc/apr.html#Linux)
[http://hi.baidu.com/injava/item/b896db21028c8e102a0f1c40](http://hi.baidu.com/injava/item/b896db21028c8e102a0f1c40)
[http://phl.iteye.com/blog/910996](http://phl.iteye.com/blog/910996)


