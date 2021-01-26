---
title: "cygwin nginx 配置 fcgi"
categories: [ "Linux" ]
tags: [ "nginx","cygwin" ]
draft: false
slug: "cygwin-nginx-fcgi-configuration"
date: "2011-09-29 09:17:00"
---

先安装nginx
1.下载fastcgi 开发包，编译安装  

    tar zvzf fcgi -current.tar.gz
    cd fcgi -current
    ./configure
    make
    make install 


<!--more-->


2.下载spawn-fcgi ，编译安装

    tar zxvf spawn-fcgi -1.6.0.tar.gz
    cd spawn-f cgi -1.6.0
        
    ./conifgure
        
    make
    make install

 

3.编写测试的c fast cgi 代码，存为 hello.c
  

    #include <fcgi_stdio.h>
       int main( int argc, char *argv[] )
      {
            while( FCGI_Accept() >= 0 ) {
            printf( "Content-Type: text/plain\n\n" );
    
            printf( "Hello FastCGI\n" );
          }
            return 0;
        }

编译c源文件，运行时有可能会报找不到lib包，这里我复制了一份，也可以做一个软链接。我这里用的是64位的机器，所以复制到了：/usr/lib64，32位的用：/usr/lib。 

`gcc -o hello hello.c -lfcgi` 

4.修改nginx.conf配置文件，重新启动nginx

 

    server {
                   listen   80;
                   server_name _;       
                   location / {
                          root   /home/user/www;
                          index  index.html;
                          fast cgi _pass 127.0.0.1:9000;
                                    }
                      } 


5. 启动hello

  
 `spawn-fcgi -a127.0.0.1 -p9000 -n ./hello`


6. 访问nginx服务器 ，浏览器 就会输出：Hello FastCGI

一个nginx c fastcgi 学习 环境搭建完毕。