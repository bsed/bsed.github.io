---
title: "linux上开发系列之 安装jdk"
categories: [ "Linux" ]
tags: [ "linux","jdk" ]
draft: false
slug: "linux-on-the-development-of-a-series-of-installation-jdk"
date: "2016-09-09 14:15:00"
---

# 安装jdk环境

服务器上如果不需要编码实际应该不安装JDK只安装JRE，我们考虑到以后可能安装其他软件就直接装JDK了。

## 下载JDK

[下载jdk](http://stackoverflow.com/questions/10268583/downloading-java-jdk-on-linux-via-wget-is-shown-license-page-instead)

上面的连接是stackoverflow有开发者写的不使用cookie下载jdk和jre的命令。


<!--more-->


```bash
[root@localhost ~]#  wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u102-b14/jdk-8u102-linux-x64.tar.gz
--2016-09-09 19:57:01--  http://download.oracle.com/otn-pub/java/jdk/8u102-b14/jdk-8u102-linux-x64.tar.gz
Resolving download.oracle.com... 23.4.240.57, 23.4.240.59
Connecting to download.oracle.com|23.4.240.57|:80... connected.
HTTP request sent, awaiting response... 302 Found
Location: http://120.52.72.24:80/download.oracle.com/c3pr90ntc0td/otn-pub/java/jdk/8u102-b14/jdk-8u102-linux-x64.tar.gz [following]
--2016-09-09 19:57:01--  http://120.52.72.24/download.oracle.com/c3pr90ntc0td/otn-pub/java/jdk/8u102-b14/jdk-8u102-linux-x64.tar.gz
Connecting to 120.52.72.24:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 181435897 (173M) [application/x-gzip]
Saving to: “jdk-8u102-linux-x64.tar.gz”

100%[==================================================================================================================================>] 181,435,897 2.07M/s   in 85s     

2016-09-09 19:58:26 (2.04 MB/s) - “jdk-8u102-linux-x64.tar.gz” saved [181435897/181435897]
```

## 解压

```bash
[root@localhost ~]# tar -zxvf jdk-8u102-linux-x64.tar.gz
[root@localhost ~]# mkdir /usr/local/java
[root@localhost ~]# mv jdk1.8.0_102/ /usr/local/java/
```

## 配置环境变量

```bash
[root@localhost ~]# vim /etc/profile
```

在最后一行添加

```bash
# java
export JAVA_HOME=/usr/local/java/jdk1.8.0_102
export JRE_HOME=/usr/local/java/jdk1.8.0_102/jre
export CLASSPATH=.:$JRE_HOME/lib/dt.jar:$JRE_HOME/lib/tools.jar
export PATH=$JRE_HOME/bin:$JRE_HOME/bin:$PATH
```

## 生效

```bash
[root@localhost ~]# source /etc/profile
[root@localhost ~]# java -version
java version "1.8.0_102"
Java(TM) SE Runtime Environment (build 1.8.0_102-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.102-b14, mixed mode)
```
这里我安装的是最新版的JDK。

Ubuntu 16.04 安装 JDK 和 Open Jdk

Ubuntu 16.04 安装 JDK 和 Open Jdk默认JDK 安装相对比较轻松，但如果想调整 OpenSdk 版本可就有点儿麻烦， 特别是 OpenJdk7 。常规安装 见这里 `java _install`
默认JDK 安装：
```
sudo apt-get update
sudo apt-get install default-jre
```
Oracle JDK 安装：
设置 PPA
```
 sudo add-apt-repository ppa:webupd8team/java
 sudo apt-get update 
```

JDK6 ：
```
 sudo apt-get install oracle-java6-installer
```

JDK 7：
```
  sudo apt-get install oracle-java7-installer
```
JDK 8
```
sudo apt-get install oracle-java8-installer
```
OpenJdk 7安装：
```
sudo add-apt-repository ppa:openjdk-r/ppa  
sudo apt-get update
sudo apt-get install openjdk-7-jdk  
```

版本之间切换
```
update-alternatives --config java
update-alternatives --config javac
```

## ceontos 

### 下载[jdk](http://pan.baidu.com/s/1lhKsu)和[jre](http://pan.baidu.com/s/1o6mfkdO),如jdk1.8.0_25和 jre1.8.0_25
### 安装
```
  rpm -ivh jdk-8u25-linux-x64.rpm
  rpm -ivh jre-8u25-linux-x64.rpm
```
### 配置环境变量
   文件默认安装于  /usr/java 下，接下来需要设置环境变量，让linux能找到jdk和jre，环境变量设置方法为（这里给所有用户设置环境变量）：
```bash 
vi /etc/profile 
```
把以下内容加入文件最后：
```bash
########## JDK #############
export JAVA_HOME=/usr/java/jdk1.8.0_25
export PATH=$PATH:$JAVA_HOME/
export CLASSPATH=.:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar
########## Jre #############
PATH=$PATH:/usr/java/jre1.8.0_25/bin
export JAVA_HOME=/usr/java/jre1.8.0_25
export CLASSPATH=$JAVA_HOME/lib
```

：wq 保存

## jdk安装还需执行：
ln -sf /opt/java/jdk1.8.0_25/bin/java /usr/bin/java
ln -sf /opt/java/jdk1.8.0_25/bin/javac /usr/bin/javac
### 重启linux完成安装。
### 测试安装结果：
分别运行一下指令测试jdk安装是否成功
```bash
echo $JAVA_HOME
echo $PATH
echo $CLASSPATH
java
javac
```
