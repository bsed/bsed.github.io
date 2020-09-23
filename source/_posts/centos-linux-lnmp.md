---
title: CentOS下用yum搭建LNMP服务器 
date: 2014-08-28 09:32:00
updated: 2014-08-28 13:50:56
tags: 
- jquery
- json
categories: 
- default

---
CentOS下搭服务器也折腾好几次了, 每次都知道个大概, 具体repo的地址什么的还都要现找, 实在不效率, 干脆整理记录下来.

yum安装比较简单快捷, 但默认配置的安全和性能方面不如LNMP一键安装包. 推荐生产环境用一键安装包

如果你在国内的话, 先替换CentOS-Base.repo


<!--more-->


    mv /etc/yum.repos.d/CentOS-Base.repo
    /etc/yum.repos.d/CentOS-Base.repo.bak
    # 如果是CentOS 5.*
    wget http://mirrors.163.com/.help/CentOS5-Base-163.repo
    # 如果是CentOS 6.*
    wget http://mirrors.163.com/.help/CentOS6-Base-163.repo

`yum makecache`

如果之前有apache, 卸载

`yum remove httpd`

更新软件到最新版本

`yum -y update`

安装源

# 如果是CentOS 5.*
    rpm -ivh http://nginx.org/packages/centos/5/noarch/RPMS/nginx-release-centos-5-0.el5.ngx.noarch.rpm
    rpm -ivh http://fedora.mirror.nexicom.net/epel/5/i386/epel-release-5-4.noarch.rpm
    rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-5.rpm
    # 如果是CentOS 6.*
    rpm -ivh http://nginx.org/packages/centos/6/noarch/RPMS/nginx-release-centos-6-0.el6.ngx.noarch.rpm
    rpm -ivh http://fedora.mirror.nexicom.net/epel/6/i386/epel-release-6-8.noarch.rpm
    rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm

编辑启用remi源

    vi /etc/yum.repos.d/remi.repo
    # 将[remi]下的enabled=0改为enabled=1
    # 按esc
    # :x回车

执行安装, 根据需要增减要安装的php模块

    yum install nginx mysql mysql-server php-fpm php-cli php-mysql php-gd php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-mcrypt php-mssql php-snmp php-soap php-tidy

启动nginx, php-fpm, mysqld

    service nginx restart
    service php-fpm restart
    service mysqld restart

设置自动启动
    
    chkconfig nginx on
    chkconfig php-fpm on
    chkconfig mysqld on

mysql密码设置

    mysql_secure_installation

至于目录权限管理什么的, 就不写了.

    niginx配置文件在/etc/nginx/nginx.conf
    php-fpm配置文件在/etc/php-fpm.conf
    php配置文件/etc/php.ini
    mysql配置文件/etc/my.cnf

参考文章:  
[http://www.ppkj.net/2011/11/18/centos6-yum-%E6%90%AD%E5%BB%BAlinux-nginx-php-mysql-lnmp/](http://www.ppkj.net/2011/11/18/centos6-yum-%E6%90%AD%E5%BB%BAlinux-nginx-php-mysql-lnmp/)  
[http://www.centos.bz/2011/03/yum-install-nginx-mysql-php-fastcgi-lnmp/](http://www.ppkj.net/2011/11/18/centos6-yum-%E6%90%AD%E5%BB%BAlinux-nginx-php-mysql-lnmp/)