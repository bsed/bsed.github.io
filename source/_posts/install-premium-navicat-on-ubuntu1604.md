---
title: 在Ubuntu16.04 上安装Navicat Premium 
date: 2016-12-03 17:41:00
updated: 2016-12-04 13:06:19
tags: 
- sqoop
- export
categories: 
- bigdata

---
> 系统: ubuntu 16.04
> 语言: English
> MYSQL: 5.7.16
> Navicat Premium: 11.2.13
## 安装 navicat-premium

首先上官网上下载LINUX版本： [https://www.navicat.com.cn/download/navicat-premium](https://www.navicat.com.cn/download/navicat-premium)

本人下载的是 这个版本的 http://download3.navicat.com/download/navicat112_premium_cs_x64.tar.gz

先看下安装后的效果吧!!@
![ubuntu_navicat_premium_00.png][1]
<!--more-->


 - 下载 `navicat112_premium_cs_x64.tar.gz` 文件
 - 下载后解压tar文件
```bash
tar -zxvf  /home/kelvin/download/navicat112_premium_cs_x64.tar.gz
```
 - 解压后  进入解压后的目录运行命令：
```bash
./start_navicat   
```
OK，这样就完啦

## 停止 mysql
```bash
service mysql stop
```

连接上数据库后里面的中文数据是乱码，如果是用的中文版，界面也会是乱码，需要修改字符集，修改方法:（`本人电脑不用修改下面的编码`）

 1. 打开start_navicat文件，会看到 `export LANG="en_US.UTF-8"` 将这句话改为 `export LANG="zh_CN.UTF-8"`
 2. 查看系统支持的字符集: locale -a  
 3. 修改字符集: `export LANG=zh_CN.utf8`  
注意：start_navicat文件和终端locale命令的返回信息这一句：`export LANG=zh_CN.utf8`一定要保持一致
 4. 还需要修改数据库字符集。gedit 打开`/etc/mysql/mysql.conf.d`路径下的`mysqld.cnf`文件，`sudo gedit /etc/mysql/mysql.conf.d/mysqld.cnf`，

在[mysqld]段落中添加如下两行：
```bash
character-set-server=utf8
collation-server=utf8_general_ci
```
## 启动mysql
```bash
service mysql start
```

然后查看数据库字符集设置：
```bash
kelvin@gnux:~> mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.16-0ubuntu0.16.14.1 (Ubuntu)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.
```

查看字符集排序设置：

```bash
mysql> show variables like 'character_set_%';
```  

如图所示：
![ubuntu_navicat_premium_01.png][2]

```bash
mysql> show variables like 'collation_%';
```   
![ubuntu_navicat_premium_02.png][3]
返回结果如图所示就大功告成了。


## 添加navicat快捷方式

在**ubuntu16** 上面下载了个`navicat`，由于此软件解压即用，所以没有图标，索性自己加一个图标，方便在`dash`里搜索

ubuntu默认的快捷方式图标在 `/usr/share/applications`

```bash
kelvin@gnux:~$ cd /usr/share/applications/
kelvin@gnux:/usr/share/applications$ sudo touch navicat.desktop
kelvin@gnux:/usr/share/applications$ sudo vi navicat.desktop
```
加入如下内容

```
[Desktop Entry]
Encoding=UTF-8
Name=Navicat Premium
Comment=The Smarter Way to manage dadabase
Exec=/bin/sh "/home/kelvin/tools/navicat112en/start_navicat"
Icon=/home/kelvin/tools/navicat112en/navicat.png
Categories=Application;Database;MySQL;navicat
Version=1.0
Type=Application
Terminal=0
```

然后在`dash`里输入`navicat`就可以看到我们刚才加的图标了

**注意**：

其中图标要自己注意路径，可以挑一张自己喜欢的图标，放在指定的位置

## 破解方案：

第一次执行start_navicat时，会在用户主目录下生成一个名为.navicat的隐藏文件夹。
```bash
cd /home/kelvin/.navicat64/
```
此文件夹下有一个`system.reg`文件

```bash
rm system.reg
```
把此文件删除后，下次启动navicat 会重新生成此文件，15天试用期会按新的时间开始计算。


  [1]: https://imgs.gnux.cn/usr/uploads/2016/12/309680100.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/12/1132722297.png
  [3]: https://imgs.gnux.cn/usr/uploads/2016/12/326139492.png