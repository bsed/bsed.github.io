---
title: Debian lnmp 安装记录
date: 2015-04-27 11:44:00
updated: 2016-12-03 11:47:01
tags: 
- hadoop
- sqoop
categories: 
- bigdata

---
## 准备安装环境  
这是所有依赖软件包集合，需要选择性安装

```
apt-get remove --purge apache2* samba* bind9* nscd
aptitude install build-essential gcc g++ cmake make ntp logrotate automake patch autoconf autoconf2.13 re2c wget flex cron libzip-dev libc6-dev rcconf bison cpp binutils tar bzip2 libncurses5-dev libncurses5 libtool libevent-dev libpcre3 libpcre3-dev libpcrecpp0 libssl-dev zlibc openssl libsasl2-dev libxml2 libxml2-dev libltdl3-dev libltdl-dev zlib1g zlib1g-dev libbz2-1.0 libbz2-dev libglib2.0-0 libglib2.0-dev libpng3 libfreetype6 libfreetype6-dev libjpeg62 libjpeg-dev libpng-dev libpng12-0 libpng12-dev libpq-dev libpq5 gettext libcap-dev ftp expect unrar gzip zip unzip git vim debian-keyring debian-archive-keyring gawk debhelper dh-systemd init-system-helpers asciidoc pkg-config xmlto apg libpcre3-dev subversion ccache xsltproc 
```


<!--more-->


## 开始安装  

```bash
aptitude install nginx-extras mariadb-client mariadb-server
aptitude install php7.0-fpm php7.0-bcmath php7.0-bz2 php7.0-cli php7.0-curl php7.0-enchant php7.0-gd php7.0-gmp php7.0-imap php7.0-interbase php7.0-intl php7.0-json php7.0-ldap php7.0-mbstring php7.0-mcrypt php7.0-memcached php7.0-mysql php7.0-odbc php7.0-opcache php7.0-pspell php7.0-readline php7.0-recode php7.0-soap php7.0-sybase php7.0-tidy php7.0-xml php7.0-xmlrpc php7.0-zip
```
## 开机启动  

```bash
systemctl enable nginx
systemctl enable mysql
systemctl enable php7.0-fpm
```
## 按需配置  

```bash
#php7
默认listen = /run/php/php7.0-fpm.sock
sed -i  s/'upload_max_filesize = 2M'/'upload_max_filesize = 1024M'/ /etc/php/7.0/fpm/php.ini
sed -i  s/'post_max_size = 8M'/'post_max_size = 1024M'/ /etc/php/7.0/fpm/php.ini
sed -i  s/'short_open_tag = Off'/'short_open_tag = On'/ /etc/php/7.0/fpm/php.ini
sed -i  s/'default_socket_timeout = 60'/'default_socket_timeout = 300'/ /etc/php/7.0/fpm/php.ini
sed -i  s/'memory_limit = 128M'/'memory_limit = 64M'/ /etc/php/7.0/fpm/php.ini
sed -i  s/';opcache.enable=0'/'opcache.enable=1'/ /etc/php/7.0/fpm/php.ini
sed -i  s/';opcache.enable_cli=0'/'opcache.enable_cli=1'/ /etc/php/7.0/fpm/php.ini
sed -i  s/';opcache.fast_shutdown=0'/'opcache.fast_shutdown=1'/ /etc/php/7.0/fpm/php.ini
sed -i  s/'zlib.output_compression = Off'/'zlib.output_compression = On'/ /etc/php/7.0/fpm/php.ini
sed -i  s/';zlib.output_compression_level = -1'/'zlib.output_compression_level = 5'/ /etc/php/7.0/fpm/php.ini
sed -i  s/'allow_url_include = Off'/'allow_url_include = On'/ /etc/php/7.0/fpm/php.ini
 
sed -i  s/'display_errors = Off'/'display_errors = On'/ /etc/php/7.0/fpm/php.ini
sed -i  s/'display_errors = On'/'display_errors = Off'/ /etc/php/7.0/fpm/php.ini
```