---
title: cygwin-easy下编译nginx
date: 2011-10-19 13:02:00
updated: 2015-02-08 14:55:56
tags: 
- nginx
- cygwin
categories: 
- linux

---
## nginx编译、构建与安装 ##

1、从[nginx.net](nginx.net)下载源码 nginx-0.7.65.tar.gz存放到D:\nginx-0.7.65\下。

2、使用cygwin把nginx安装到d:/nginx。


<!--more-->


安装命令如下：

    cd D:

    tar zxvf nginx-0.7.65.tar.gz

    cd nginx-0.7.65

    ./configure --prefix=/cygdrive/d/nginx --sbin-path=/cygdrive/d/nginx --with-cc-opt="-D FD_SETSIZE=4096" --with-rtsig_module --with-select_module --with-poll_module --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_stub_status_module --with-mail --with-cpp_test_module --with-mail_ssl_module
    
    make && make install

执行完第4步：得到一个Makefile文件与objs子目录下的多个文件。【--with-http_xslt_module】

执行完第5步的make命令，这时cd进去objs子目录，然后ls一下，看到有nginx.exe文件。这就是编译构建后生成的nginx程序。make install命令是安装程序到指定的目录中。

如果不加--with-cc-opt=”-DFD_SETSIZE=4096”的话，你装好nginx后运行会出现

   2008/01/12 16:34:56 [emerg] 2496#0: the maximum number of files supported by select() is 64的错误提示，这表示FD_SETSIZE的值比nginx配置文件中worker_connections指令所指定的值，你可以把nginx.conf里的worker_connections选项改小一些，比如44,加了--with-cc-opt=”-D FD_SETSIZE=4096”后就不会碰到这问题

上面的第4、5步每次都要输入这么多命令，调试时非常麻烦。有一个办法可以减少工作量。就是在nginx-0.7.65目录中，新建一个记事本文件，里面内容为：

    ./configure --prefix=/cygdrive/d/nginx --sbin-path=/cygdrive/d/nginx --with-cc-opt="-D FD_SETSIZE=4096" --with-rtsig_module --with-select_module --with-poll_module --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_stub_status_module --with-mail --with-cpp_test_module--with-mail_ssl_module;make && make install

然后把这个记事本文件改名为makeexe，保存文件。注意没有后缀名。

在cygwin窗口中执行sh makeexe，相当于执行第4、5步。

nginx运行
把nginx运行时所需要用到的DLL文件找出来，(这些文件都在cygwin安装目录的bin子目录下)，复制到d:/nginx目录下。所需文件如下图所示：
