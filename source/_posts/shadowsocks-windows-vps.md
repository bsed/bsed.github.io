---
title: ShadowSocks教程:在windows VPS下搭建shadowsocks服务端
date: 2009-04-06 11:45:00
updated: 2014-11-08 18:18:04
tags: 
- windows
- less
categories: 
- js

---
ShadowSocks教程:在windows VPS 下搭建 shadowsocks服务端
之前放了几个在LINUX下搭建SS的服务教程，包括优化等。如果你手上的是WIN系统的VPS的话，建shadowsocks还是更简单的。
方法一：
去官网 shadowsocks => [https://github.com/clowwindy/shadowsocks](https://github.com/clowwindy/shadowsocks)

<!--more-->

上下载WIN服务端的文档  shadowsocks_win_server.zip
在windows服务器上做shadowsocks服务端相当简单，解压shadowsocks_win_server.zip，在config.json文件里设置好端口、密码、加密方式以后，运行shadowsocks.exe就OK了，然后会最小化到系统托盘。
如果更改了配置文件，需要右键系统托盘客户端，重新加载即可生效。
最后说一下配置文件里的加密方式，”method”: “aes-256-cfb“, 最推荐，程序默认的加密方式为table，不安全，但理论上速度最快。客户端连接服务器的时候也要对应上相应的加密方式，否则连不上！
方法二 ：
V2EX上的Gramsight写的教   程windows server 2012 r2 的 vps为例
来源：
[http://www.v2ex.com/t/124728](https://github.com/clowwindy/shadowsocks)
准备工作：

>  1. python 2.7 32bit => https://www.python.org/download/releases/2.7.8/
>  2. pip => https://pip.pypa.io/en/latest/installing.html
>  3. Visual C++ 2008 Redistributables =>http://www.microsoft.com/en-us/download/details.aspx?id=29
>  4. OpenSSL Win-32bit => http://slproweb.com/products/Win32OpenSSL.html
>  5. OpenSSL Source => https://www.openssl.org/source/
>  6. MinGW 32bit => http://sourceforge.net/projects/mingw/files/
>  7. SWIG => http://sourceforge.net/projects/swig/
>  8. shadowsocks => https://github.com/clowwindy/shadowsocks

注意：下面使用的[路径]仅供参考。实际路径请根据自己的环境来更变。
操作步骤：
安装 python 2.7 32bit
把 [C:\Python27] 加入到 环境变量 path 中
安装 pip => `python get-pip.py`
把 [C:\Python27\Scripts] 加入到 环境变量 path 中
安装 Visual C++ 2008 Redistributables。如果已经安装的可以跳过
安装 OpenSSL Win-32bit
如果安装时选择 [Copy OpenSSL DLLs to: -> The OpenSSL binaries(/bin) directory] 的，需要自己手动把 [C:\OpenSSL-Win32\bin] 加入到 环境变量 path 中
安装 MinGW 32bit
成功安装好后，会弹出 MinGW Installation Manager ，然后把 Basic Setup 全部 Mark for Installation ，最后点击菜单栏 [Installation -> Apply Changes -> Apply]
把 [C:\OpenSSL-Win32\bin] 下的 libeay32.dll 和 ssleay32.dll 复制到 [C:\MinGW\mingw32\lib\] 下
把 [C:\MinGW\bin] 加入到 环境变量 path 中
解压 SWIG
把 [C:\swigwin] 加入到 环境变量 path 中
注销或者重启 windows。让 path 生效
将 OpenSSL Source 复制到 [C:\MinGW\msys\1.0\home\用户名\] 。 ps:如果没有这个目录，可以通过打开[C:\MinGW\msys\1.0\msys.bat]，来创建。
打开 [C:\MinGW\msys\1.0\msys.bat]
输入 tar zxf openssl-1.0.1h.tar.gz 注意：openssl-1.0.1h.tar.gz 为下载的 OpenSSL Source 包
输入 cd openssl-1.0.1h
输入 ./config --prefix="$HOME/prebuilt" --openssldir="$HOME/prebuilt/openssl"
输入 make && make install
创建文件夹 [C:\pkg]
把 [C:\MinGW\msys\1.0\home\用户名\prebuilt\] 下的所有文件复制到 [C:\pkg\]
在 [C:\Python27\Lib\distutils\] 下，创建 distutils.cfg
在 distutils.cfg 中写入
[build]
compiler=mingw32
运行 cmd
输入 pip install m2crypto
用git clone shadowsocks 或者 直接下载 源代码
编辑 [C:\Repository\shadowsocks\shadowsocks\encrypt.py]
在 line 29 之后插入 import M2Crypto
保存 encrypt.py
运行 cmd
输入 cd "C:\Repository\shadowsocks"
输入 python setup.py build
输入 python setup.py install
之后就可以在 [C:\Python27\Scripts\] 下找到 shadowsocks 程序。详细使用说明可以参考官方文档。
**可能会遇到的问题：**
如果运行报错 ImportError: DLL load failed: The specified procedure could not be found.
将 [C:\OpenSSL-Win32\bin\] 下的 libeay32.dll 和 ssleay32.dll 复制到 [C:\Python27\Scripts\] 下
或者
将 [C:\OpenSSL-Win32\bin\] 下的 libeay32.dll 和 ssleay32.dll 复制到 [C:\Windows\System32\] 下
或者
重新安装 OpenSSL Win-32bit 选择 [Copy OpenSSL DLLs to: -> The Windows system directory]
最后再放一个官网 Shadowsocks  地址：
项目地址：[https://github.com/clowwindy/shadowsocks](https://github.com/clowwindy/shadowsocks)
中文版使用说明：[https://github.com/clowwindy/shadowsocks/wiki/Shadowsocks-%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E](https://github.com/clowwindy/shadowsocks/wiki/Shadowsocks-%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)