---
title: "linux下火狐浏览器启用jre"
categories: [ "Linux" ]
tags: [ "firefox","ubuntu","jdk" ]
draft: false
slug: "the-firefox-browser-enabled-jre-linux"
date: "2013-07-22 08:29:00"
---

     linux下的火狐默认是没有启用jre的，如果想启用jre，要先安装jdk，之后按照下面的方法启用。注意JDK的路径，系统是i386和amd64对应改好目录 

*3.6之前版本*

    sudo ln -s /opt/java/jdk_1.6.0.30/jre/plugin/i386/ns7/libjavapluginoji.so  /usr/lib/mozilla/plugins

*3.6之后的版本*


<!--more-->


     ln -s  /opt/java/jdk_1.6.0.30/jre/lib/amd64/libnpjp2.so /usr/lib/mozilla/plugins


现在一般都是3.6之后的火狐了，安装成功后可以在附加组件中看到java相关的插件，在地址栏中输入 about:plugins也可以看到java。

ps: 由于java的漏洞比较多，如果不是必要，强烈不建议在浏览器中启用java。如果真需要使用可以临时启用，用完后在取消。


