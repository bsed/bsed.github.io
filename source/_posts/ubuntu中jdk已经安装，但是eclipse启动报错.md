---
title: "ubuntu中jdk已经安装，但是eclipse启动报错"
categories: [ "Java" ]
tags: [ "ubuntu","eclipse","jdk" ]
draft: false
slug: "jdk-eclipse-is-already-installed-but-the-ubuntu-starts-the-report"
date: "2012-11-08 16:02:00"
---

## 问题描述

在ubuntu中，jdk已经正常安装，java_home变量已经配置，但是启动eclipse的时候还是弹出以下错误信息：

> A Java RunTime Environment (JRE) or Java Development Kit (JDK) must be
> available in order to run Eclipse.No java virtual machine was found
> after searching the following
> locations：/opt/zzjxkhgl/liferay-ide-eclipse/jre/bin/java java in your
> current PATH

## 解决办法


<!--more-->


1、在eclipse目录中新建jre目录

    mkdir jre

2、在jre中建立java软链接

    cd jre
    ln -s /opt/jdk1.6.0_45/bin/ bin

3、重启启动eclipse，问题解决。