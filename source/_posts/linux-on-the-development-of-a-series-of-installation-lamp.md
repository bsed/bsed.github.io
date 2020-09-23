---
title: linux上开发系列之安装LAMP
date: 2016-10-29 20:01:00
updated: 2016-11-06 16:14:22
tags: 
- linux
- fcitx
- emacs
categories: 
- vim

---
#  Ubuntu 系统

## 安装APACHE

```bash
$ sudo apt install apache2
OR
$ sudo apt-get install apache2
```
重启服务:

```bash
$ sudo systemctl status apache2.service 
$ sudo netstat –tlpn
```
如图：
![Apache-Default-Page.png][1]
## 安装php
```

开启ssl:

```bash
$ sudo a2enmod ssl 
$ sudo a2ensite default-ssl.conf 
$ sudo systemctl restart apache2.service
```
查看ssl 端口

```bash
sudo netstat -tlpn
```


### 首先增加php7 仓库

```php
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:ondrej/php-7.0
```

### 移除php5，安装php7

安装前做好 配置文件备份工作。


<!--more-->


```bash
sudo apt-get update && sudo apt-get purge php5-fpm && sudo apt-get --purge autoremove && sudo apt-get install php7.0-fpm php7.0-mysql
```

### 禁用php5

```bash
sudo a2dismod php5
```
参考php5，创建php7配置文件

```bash
sudo cp /etc/apache2/mods-available/php5.conf /etc/apache2/mods-enabled/php7.conf
sudo a2enmod php7
sudo service apache2 restart
```

## php7 curl 安装
```
sudo apt-get install php7.0-curl
```
重启Apache

```bash
$ sudo systemctl restart apache2
```
## 安装MariaDB 

```bash
$ sudo apt install php7.0-mysql mariadb-server mariadb-client
```

配置MariaDB

```bash
 sudo mysql_secure_installation
```

![Secure-MariaDB-in-Ubuntu-16.04.png][2]

连接数据库:

```bash
$ sudo mysql 
MariaDB> use mysql;
MariaDB> update user set plugin='' where User='root';
MariaDB> flush privileges;
MariaDB> exit
```

如图：

![Assign-User-Permissions-to-MariaDB.png][3]

重启mysql:

```bash
$ sudo systemctl restart mysql.service
$ mysql -u root -p
```
参考：

 - [https://www.linuxbabe.com/linux-server/install-apache-mariadb-and-php7-lamp-stack-on-ubuntu-16-04-lts](https://www.linuxbabe.com/linux-server/install-apache-mariadb-and-php7-lamp-stack-on-ubuntu-16-04-lts)

 - [https://www.linuxbabe.com/linux-server/install-nginx-mariadb-php7-lemp-stack-ubuntu-16-04-lts](https://www.linuxbabe.com/linux-server/install-nginx-mariadb-php7-lemp-stack-ubuntu-16-04-lts)
  [1]: https://imgs.gnux.cn/usr/uploads/2016/11/4013838999.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/11/2079673821.png
  [3]: https://imgs.gnux.cn/usr/uploads/2016/11/2571208965.png