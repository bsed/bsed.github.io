---
title: Tomcat下内存溢出的解决办法
date: 2012-03-03 01:09:00
updated: 2015-10-01 09:05:45
tags: 
- firefox
- icon
categories: 
- css

---
tomcat服务下报java.lang.OutOfMemoryError: PermGen space异常

## 引发的原因：

java虚拟机jvm的内存不够：

**解决办法一**：JDK内存优化（Tomcat默认可以使用的内存为128MB）

windows环境下，tomcat的安装目录下bin/catalina.bat文件

rem Guess CATALINA_HOME if not defined
set CURRENT_DIR=%cd%后面添加,红色的为新添加的.

    set JAVA_OPTS=-Xms256m -Xmx512m -XX:PermSize=128M -XX:MaxNewSize=256m -XX:MaxPermSize=256m -Djava.awt.headless=true


<!--more-->


（其中：-Xms
JVM初始化堆的大小

-Xmx
JVM堆的最大值）

Unix和linux下 tomcat的安装目录下bin/catalina.sh文件

第一行增加：

JAVA_OPTS='-Xms256m -Xmx512m' 

## 二： java.lang.OutOfMemoryError: PermGen space PermGen space 

在tomcat的安装目录下bin/catalina.sh文件中：


位置cygwin=false前 。注意引号要带上,红色的为新添加的.
```
# OS specific support. $var _must_ be set to either true or false.
JAVA_OPTS="-Xms256m -Xmx512m -Xss1024K -XX:PermSize=128m -XX:MaxPermSize=256m"
cygwin=false
```