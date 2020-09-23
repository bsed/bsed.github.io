---
title: python在window下的Nginx部署
date: 2015-02-06 11:09:00
updated: 2015-02-07 11:12:08
tags: 
- easy_install
- cygwin
categories: 
- python

---
Python版本3.2

1 安装nginx

下载windows上的nginx最新版本，http://www.nginx.org/en/download.html。
解压后即可。
运行nginx.exe后本地打开http://localhost，将会看到欢迎页面，这证明nginx在本地运行良好，如果没有出现欢迎页面，请检查是否有进程占用了80端口。


<!--more-->


2 安装flup

下载对应版本的flup，这里下载flup3.x版本，适合python3.2，下载地址：https://github.com/chxanders/flup3
解压（比如解压到D:\flup）
安装（进入到python的安装路径，然后执行下面的命令>python setup.py install）
！注意，如果提示缺少setuptools，安装distribute-0.6.49.tar.gz，安装方法和flup安装一样，下载地址：https://pypi.python.org/pypi/distribute/0.6.49

3 安装Mysql

在这里我使用的是5.1版本。在win系统上双击安装文件，下一步下一步完成。下载地址：http://dev.mysql.com/downloads/

4 安装数据库驱动

下载用于win上对应py版本的的python-mysql驱动，双击安装即可。下面的下载地址是3.2：

[http://files.cnblogs.com/wangqc/distribute-0.6.49.zip](http://files.cnblogs.com/wangqc/distribute-0.6.49.zip)

5 配置服务器

首先需要修改nginx的配置文件nginx.conf。
找到：

    location / {
    root html;
    index index.html index.htm;
    }

在里面加上：

    # host and port to fastcgi server
    fastcgi_pass 127.0.0.1:55880;
    fastcgi_param PATH_INFO $fastcgi_script_name;
    fastcgi_param REQUEST_METHOD $request_method;
    fastcgi_param QUERY_STRING $query_string;
    fastcgi_param SERVER_NAME $server_name;
    fastcgi_param SERVER_PORT $server_port;
    fastcgi_param SERVER_PROTOCOL $server_protocol;
    fastcgi_param CONTENT_TYPE $content_type;
    fastcgi_param CONTENT_LENGTH $content_length;
    fastcgi_pass_header Authorization;
    fastcgi_intercept_errors off; 

然后测试该配置文件是否正确，在cmd中切换到nginx安装目录里，输入nginx.exe -t即可开始对配置文件测试，如果提示成功，说明配置正确，
这是可以结束掉任务管理器中所有的nginx.exe进程，重新运行ngin.exe重启nginx服务。

6 运行Server.py
在cmd下切换到项目目录，输入命令python Server.py runfcgi method=threaded host=127.0.0.1 port=55880 注意，
这条命令只能用来启动项目，如果出现错误并不会给出提示。下面是正确的情况.

pythonAppendCmd
用浏览器打开http://127.0.0.1:8080 测试一下，项目是不是已经跑起来了（第一次运行等待的时间稍长，请耐心等待）。