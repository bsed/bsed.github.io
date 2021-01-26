---
title: "让Windows版Apache支持IPv6"
categories: [ "Java","PHP" ]
tags: [ "Apache","ipv6" ]
draft: false
slug: "let-the-windows-version-of-apache-that-supports-ipv6"
date: "2012-05-02 11:34:00"
---

![apache_ipv6.jpg][1]
最近需要搭建支持IPv6的Windows Server，故有此篇文章。

很多人都遇到了在Windows下安装Apache不支持IPv6访问的问题，通过两种办法可以解决这一问题：一是自己编译，二是从网上寻找现成的版本。自己编译我嫌麻烦，也没有研究过，有兴趣的可以去Demon的博客看看，那里有说如何编译使Windows下的Apache支持IPv6。而在网上寻找现成版本，最早找到的是XAMPP，它的新版本支持IPv6，但是XAMPP所带的PHP版本与我锁需要的版本不一样，有些PHP扩展无法支持，并且XAMPP的PHP和Apache都是它自己编译的，修改了很多东西，通用性较差。最终在Apache Haus上找到了Windows下支持IPv6第三方编译版本。


<!--more-->


下载地址：

[http://www.apachehaus.com/cgi-bin/download.plx](http://www.apachehaus.com/cgi-bin/download.plx)

——————————————————————————-

目前支持IPv6的最新版本是Apache 2.2.15，32位和64位的版本都有，下载的时候注意“IPv6 Enabled”字样，别下错了。

另外还需要下载安装Microsoft Visual C++ 2008 Redistributable Package，这个网站编译版本需要安装这个东东。

下载地址：

[Microsoft Visual C++ 2008 Redistributable Package (x86)](http://www.microsoft.com/downloads/details.aspx?FamilyID=9B2DA534-3E03-4391-8A4D-074B9F2BC1BF&displaylang=en)

[Microsoft Visual C++ 2008 Redistributable Package (x64)](http://www.microsoft.com/downloads/details.aspx?familyid=bd2a6171-e2d6-4230-b809-9a8d7548c1b6&displaylang=en)

安装方法：

1.安装Visual C++ 2008 Redistributable Package

2.将目录Apache2解压至C盘根目录下（因为配置文件中默认目录为c:/Apache2，如需要放在其他目录，请修改配置文件）。

3.打开命令行模式，并且进入Apache所在目录（如C:\Apache2），输入httpd.exe（回车）即可启动Apache

如果需要将Apache安装为服务，运行输入httpd.exe -k install（回车）

启动服务httpd.exe -k start（回车）

停止服务httpd.exe -k stop（回车）

重启服务httpd.exe -k restart（回车）

卸载服务httpd.exe -k uninstall（回车）

其实和官方编译的Apache使用完全一样。


  [1]: https://imgs.gnux.cn/usr/uploads/2015/03/528100296.jpg