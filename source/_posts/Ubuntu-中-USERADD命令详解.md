---
title: "Ubuntu 中 USERADD命令详解"
categories: [ "Linux" ]
tags: [ "linux","useradd" ]
draft: false
slug: "useradd-command"
date: "2015-11-01 13:50:00"
---

## 1.作用
useradd命令用来建立用户帐号和创建用户的起始目录，使用权限是终极用户。
## 2.格式
useradd [－d home] [－s shell] [－c comment] [－m [－k template]] [－f inactive] [－e expire ] [－p passwd] [－r] name
## 3.主要参数
－c：加上备注文字，备注文字保存在passwd的备注栏中。 
－d：指定用户登入时的启始目录。
－D：变更预设值。
－e：指定账号的有效期限，缺省表示永久有效。
－f：指定在密码过期后多少天即关闭该账号。
－g：指定用户所属的起始群组。
－G：指定用户所属的附加群组。
－m：自动建立用户的登入目录。
－M：不要自动建立用户的登入目录。
－n：取消建立以用户名称为名的群组。
－r：建立系统账号。
－s：指定用户登入后所使用的shell。
－u：指定用户ID号。
## 4.说明


<!--more-->


`useradd`可用来建立用户账号，他和adduser命令是相同的。账号建好之后，再用passwd设定账号的密码。使用useradd命令所建立的账号，实际上是保存在/etc/passwd文本文件中。
## 5.应用实例

建立一个新用户账户，并设置ID：

    ＃useradd david -u 544

需要说明的是，设定ID值时尽量要大于500，以免冲突。因为Linux安装后会建立一些特别用户，一般0到499之间的值留给bin、mail这样的系统账号。

    ＃useradd oracle -g oinstall -G dba

新创建一个oracle用户，这初始属于oinstall组，且同时让他也属于dba组。

＃useradd tomcat  -d /var/servlet/service -s /sbin/nologin

无法使用shell，且其用户目录至/var/servlet/service
