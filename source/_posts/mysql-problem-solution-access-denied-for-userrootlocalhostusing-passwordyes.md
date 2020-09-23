---
title: MYSQL问题解决方案：Access denied for user 'root'@'localhost' (using password:YES)
date: 2014-01-22 10:11:00
updated: 2017-06-21 10:11:39
tags: 
- golang
- xml
categories: 
- go

---
连接MySQL数据库，出现问题：Access denied for user 'root'@'localhost' (using password:YES)。
经查找资料发现是root帐户默认不开放远程访问权限，所以需要修改一下相关权限。

## 解决方案：

打开MySQL目录下的my.ini文件，在文件的最后添加一行`skip-grant-tables`，保存并关闭文件。（WIN7默认安装，my.ini在C:\ProgramData\MySQL\MySQL Server 5.6）


<!--more-->


## 重启MySQL服务。

通过命令行进入MySQL的BIN目录，输入`mysql -u root -p`(不输入密码)，回车即可进入数据库。（WIN7默认安装，BIN目录为：C:\Program Files\MySQL\MySQL Server 5.6\bin）

执行`use mysql;，使用mysql数据库。

执行`update user set password=PASSWORD("123456") where user='root';`（修改root的密码）

打开MySQL目录下的my.ini文件，删除最后一行的`skip-grant-tables`，保存并关闭文件。

## 重启MySQL服务。

在命令行中输入`mysql -u root -p 123456`，即可成功连接数据库。
完成以上步骤，dbever 就可成功连接mysql了。