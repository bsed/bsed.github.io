---
title: "Install Nginx+PHP on Amazon EC2 Centos"
categories: [ "Linux" ]
tags: [ "php","nginx" ]
draft: false
slug: "amazon-ec2-linux-nginx-php-centos"
date: "2014-12-29 19:12:00"
---

安装Nginx  （http://admclub.com/view/linux-nginx-php-mysql-memcached-eaccelerator-%E5%AE%89%E8%A3%85%E4%BC%98%E5%8C%96%E8%AE%B0%E5%BD%95）

先安装依赖库 

    yum install  autoconfig automake make gcc gcc-c++ \
    pcre-devel openssl-devel patch pam-devel libmcrypt-devel gd-devel -y

安装 libunwind
找到最新版本 ：[http://download.savannah.gnu.org/releases/libunwind/](http://download.savannah.gnu.org/releases/libunwind/ )


<!--more-->


    cd /usr/local/src
    wget http://download.savannah.gnu.org/releases/libunwind/libunwind-1.0.1.tar.gz
    tar xzfv libunwind-1.0.1.tar.gz
    cd libunwind-1.0.1
    ./configure
    make && make install

安装 gperftools
找到最新版本 ：http://code.google.com/p/gperftools/downloads/list

    cd ..
    wget http://gperftools.googlecode.com/files/gperftools-2.0.tar.gz
    tar xzfv gperftools-2.0.tar.gz
    cd gperftools-2.0
    ./configure --enable-frame-pointers
    make && make install

安装Nginx
找到最新版本 ：http://nginx.org/en/download.html

    cd ..
    wget http://nginx.org/download/nginx-1.0.11.tar.gz
    tar zxfv nginx-1.0.11.tar.gz 
    cd nginx-1.0.11
    nano auto/cc/gcc 修改 #debug 段 CFLAGS ，注释掉。
    cd ../..
    sudo ./configure --with-file-aio --with-ipv6 --with-rtsig_module --with-select_module --with-poll_module --with-ipv6 --with-http_sub_module --with-http_ssl_module --with-http_image_filter_module --with-http_dav_module --with-http_gzip_static_module --with-http_addition_module --with-zlib= --with-md5= --with-sha1= --with-google_perftools_module
    make && make install

配置Nginx

    echo "/usr/local/lib" > /etc/ld.so.conf.d/usr_local_lib.conf
    /sbin/ldconfig
    
    mkdir /tmp/tcmalloc
    chmod 0777 /tmp/tcmalloc

修改`/usr/local/nginx/conf/ncing.conf`
在pid这行的下面添加

    #pid        logs/nginx.pid;
    google_perftools_profiles /tmp/tcmalloc/;

启动Nginx

    /usr/local/nginx/sbin/nginx


安装 php 及 php-fpm

安装依赖库

    yum groupinstall "Development Tools"
    yum groupinstall "Development Libraries"
    sudo yum install libxml2-devel curl-devel libicu-devel libxml2-devel libmcrypt.x86_64 libmcrypt-devel.x86_64 php-mysql.x86_64 mysql-devel.x86_64 curl-devel libc-client-devel 
    
    yum install libxml2-devel.x86_64 openssl-devel.x86_64 bzip2-devel.x86_64 curl-devel.x86_64 db4-devel.x86_64 \
    libjpeg-devel.x86_64 libpng-devel.x86_64 libXpm-devel.x86_64 freetype-devel.x86_64 gmp-devel.x86_64 \
    libc-client-devel.x86_64 openldap-devel.x86_64 libmcrypt-devel.x86_64 libmhash-devel.x86_64 freetds-devel.x86_64 \
    libz-devel.x86_64 mysql-devel.x86_64 ncurses-devel.x86_64 pcre-devel.x86_64 unixODBC-devel.x86_64 postgresql-devel.x86_64 \
    sqlite-devel.x86_64 aspell-devel.x86_64 readline-devel.x86_64 recode-devel.x86_64 net-snmp-devel.x86_64 \
    libtidy-devel.x86_64 libxslt-devel.x86_64 t1lib-devel.x86_64 libicu libicu-devel.x86_64




找到最新版本 ：http://cn.php.net/get/php-5.3.10.tar.gz/from/jp2.php.net/mirror

---------------------------------------------------------------------------------------------------

输出目录：

    Installing PHP SAPI module:       fpm
    Installing PHP CLI binary:        /usr/local/bin/
    Installing PHP CLI man page:      /usr/local/man/man1/
    Installing PHP FPM binary:        /usr/local/sbin/
    Installing PHP FPM config:        /usr/local/etc/
    Installing PHP FPM man page:      /usr/local/man/man8/
    Installing PHP FPM status page:      /usr/local/share/php/fpm/
    Installing build environment:     /usr/local/lib/php/build/
    Installing header files:          /usr/local/include/php/
    Installing helper programs:       /usr/local/bin/
      program: phpize
      program: php-config
    Installing man pages:             /usr/local/man/man1/
      page: phpize.1
      page: php-config.1
    Installing PEAR environment:      /usr/local/lib/php/
    [PEAR] Archive_Tar    - installed: 1.3.7
    [PEAR] Console_Getopt - installed: 1.3.0
    [PEAR] Structures_Graph- installed: 1.0.4
    [PEAR] XML_Util       - installed: 1.2.1
    [PEAR] PEAR           - installed: 1.9.4
    Wrote PEAR system config file at: /usr/local/etc/pear.conf
    You may want to add: /usr/local/lib/php to your php.ini include_path
    /usr/local/src/php-5.3.10/build/shtool install -c ext/phar/phar.phar /usr/local/bin
    ln -s -f /usr/local/bin/phar.phar /usr/local/bin/phar
    Installing PDO headers:          /usr/local/include/php/ext/pdo/

------------------------------------------------------------------------------------------------------

    [root@ip-10-146-57-65 php-5.3.10]# useradd www
    [root@ip-10-146-57-65 php-5.3.10]# mkdir /var/www
    [root@ip-10-146-57-65 php-5.3.10]# chmod 755 /var/www
    [root@ip-10-146-57-65 php-5.3.10]# chown -R www:www /var/www
    
    
    vi /etc/php.ini  cgi.fix_pathinfo=0
    cp /usr/local/etc/php-fpm.conf.default /usr/local/etc/php-fpm.conf
    
    /usr/local/sbin/php-fpm
    /usr/local/nginx/sbin/nginx -s reload

