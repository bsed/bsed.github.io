---
title: Mac下使用brew安装 mysql
date: 2020-08-02 19:37:00
updated: 2020-08-02 19:38:05
tags: 
- mac
- docker
categories: 
- os

---
通过homebrew安装mysql，目前默认安装最新版本，要安装其他版本，需指定版本号。

```bash
brew  install mysql@5.6
```


<!--more-->


## 修改配置文件

`vim /usr/local/etc/my.cnf`
```bash
# Default Homebrew MySQL server config
[client]
port = 3306
socket = /tmp/mysql.sock
default-character-set = utf8

[mysqld]
collation-server = utf8_unicode_ci
character-set-server = utf8
init-connect ='SET NAMES utf8'
max_allowed_packet = 64M
bind-address = 127.0.0.1
port = 3306
socket = /tmp/mysql.sock
skip-grant-tables
```
## 启动服务
```bash
brew services start mysql@5.6 
brew services restart mysql@5.6  #重启
brew services stop mysql@5.6     #停止
```
## 连接数据库
```bash
mysql -u root -p
```