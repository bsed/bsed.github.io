---
title: 采用lighttpd+fastcgi用C/C++语言写自己的服务程序
date: 2014-02-12 10:06:00
updated: 2015-02-09 10:09:18
tags: 
- nodejs
- cygwin
categories: 
- linux

---
有项目需要建一个WEB服务器，并在WEB服务器中要调用自己的库，就花了点时间用C语言写的fastcgi简单试了一下。这种方案的问题是C语言要自己输出网页，耦合性比较强，如果网页比较复杂的话就容易出错，所以比较适合网页不太复杂的情况。 当然如果自己的后台服务及接口非常复杂，为了独立性，可将自己的服务写成一个网络服务器，类似mysql，其它如php，java等服务端程序直接通过socket交换数据，这样将数据及页面生成分离，扩展性会更加好。

下面是用[lighttpd](http://www.lighttpd.net/)和C语言实现的简单fastcgi测试步骤，采用unix domain socket方式的方式通讯。 下载编译fastcgi C语言开发包：


<!--more-->


[http://www.fastcgi.com/dist/fcgi.tar.gz](http://www.fastcgi.com/dist/fcgi.tar.gz) 下载的这个库可能需要改个BUG: 在源码`/include/fcgio.h`文件中加上 `#include <cstdio>`解决编译错误的问题。

    ./configure
    make
    sudo make install

编辑test.c程序 此程序输出HTTP环境变量

    #include <syslog.h>
    #include <alloca.h>
    #include <fcgiapp.h>
    #define LISTENSOCK_FILENO 0
    #define LISTENSOCK_FLAGS 0
    int main(int argc, char** argv) {
      openlog ("testfastcgi", LOG_CONS|LOG_NDELAY, LOG_USER);
      int err = FCGX_Init (); /* call before Accept in multithreaded apps */
      if (err) { syslog (LOG_INFO, "FCGX_Init failed: %d", err); return 1; }
      FCGX_Request cgi;
      err = FCGX_InitRequest(&cgi, LISTENSOCK_FILENO, LISTENSOCK_FLAGS);
      if (err) { syslog (LOG_INFO, "FCGX_InitRequest failed: %d", err); return 2; }
      while (1) {
        err = FCGX_Accept_r(&cgi);
        if (err) { syslog (LOG_INFO, "FCGX_Accept_r stopped: %d", err); break; }
        char** envp;
        int size = 200;
        for (envp = cgi.envp; *envp;   envp) size  = strlen(*envp)   11;
        char*  result = (char*) alloca(size);
        strcpy (result, "Status: 200 OKrnContent-Type: text/htmlrnrn");
        strcat (result, "<html><head><title>testcgi</title></head><body><ul>rn");
        for (envp = cgi.envp; *envp;   envp) {
          strcat(result, "<li>");
          strcat(result, *envp);
          strcat(result, "</li>rn");
        }
        strcat(result, "</ul></body></html>rn");
        FCGX_PutStr(result, strlen(result), cgi.out);
        FCGX_Finish_r(&cgi);
      }
      FCGX_ShutdownPending();
      return 0;
    }

编译并生成test.mycgi

`gcc test.c -lfcgi -o test.mycgi`

安装lighttpd 

`sudo apt-get install lighttpd`

打开fastcgi模块

`sudo lighty-enable-mod fastcgi`

编辑配置文件/etc/lighttpd/lighttpd的static-file.exclude-extensions，添加fastcgi的程序后缀名如.mycgi
`static-file.exclude-extensions = ( ".php", ".pl", ".fcgi", ".mycgi")`
配置

让`/home/test/www/test.mycgi`程序去处理/test下访问的请求

    fastcgi.server = ("/test"=>
    ((
            "socket"=>"/tmp/test.fcgi.socket",
            "check-local"=>"disable",
            "bin-path"=>"/home/test/www/test.mycgi"
    ))
    )

帮助见此：[http://redmine.lighttpd.net/projects/lighttpd/wiki/Docs_ModFastCGI](http://redmine.lighttpd.net/projects/lighttpd/wiki/Docs_ModFastCGI)

启动lighttpd

`sudo service lighttpd force-reload`
访问http://localhost/test 看结果吧。

另：C++版本的开发库：fastcgi++

[http://www.nongnu.org/fastcgipp/](http://www.nongnu.org/fastcgipp/)

编译时需要boost开发库

`sudo apt-get install libboost-all-dev`
另外发现有嵌入式的http服务器：

[http://www.gnu.org/software/libmicrohttpd/](http://www.gnu.org/software/libmicrohttpd/)
