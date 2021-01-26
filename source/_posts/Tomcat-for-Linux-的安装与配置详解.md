---
title: "Tomcat for Linux 的安装与配置详解"
categories: [ "Linux" ]
tags: [ "tomcat8" ]
draft: false
slug: "tomcat-for-linux-detailed-installation-and-configuration"
date: "2013-09-16 09:21:00"
---

# 1.下载

到官网 [http://jakarta.apache.org/tomcat](http://jakarta.apache.org/tomcat) 下载 apache-tomcat-8.0.24-src.tar.gz 安装文件，JDK假设已经安装完毕，接下来直接安装Tomcat。
# 2.安装

    # tar -zxvf apache-tomcat-8.0.24-src.tar.gz
    # ls //会发现在当前目录下，多出一个文件夹
    apache-tomcat-8.0.24-src.tar.gz apache-tomcat-8.0.24-src
    # mv apache-tomcat-8.0.24-src /usr/local/tomcat8


<!--more-->


# 3.设置环境变量

    # vi ~/.bashrc
    
    if [ -f /etc/bashrc ]; then
    . /etc/bashrc
    fi

//增加下面两个内容，然后保存退出：wq 回车

    export TOMCAT_HOME=/usr/local/tomcat8
    export CATALINA_BASE=/usr/local/tomcat8
    export CATALINA_HOME=/usr/local/tomcat8

验证是否安装成功

    # echo $CATALINA_HOME
    /usr/local/tomcat8
    # echo $CATALINA_BASE
    /usr/local/tomcat8

如果显示上面内容，OK，已生效。如果不显示，重启一下系统，再试。

# 4.启动服务

我们先看一下linux所开的端口：

    # netstat –l
    # cd /usr/local/tomcat-7.0.2／bin
    # ls
    catalina.bat        configtest.sh  setclasspath.bat  startup.sh
    catalina.sh         daemon.sh      setclasspath.sh   tool-wrapper.bat
    catalina.sh~        digest.bat     shutdown.bat      tool-wrapper.sh
    catalina-tasks.xml  digest.sh      shutdown.sh       version.bat
    configtest.bat      service.bat    startup.bat       version.sh

//我们要注意这个文件:startup.sh

    root@gnux:~/tools/tomcat8/bin# ./startup.sh
    Using CATALINA_BASE:   /root/tools/tomcat8
    Using CATALINA_HOME:   /root/tools/tomcat8
    Using CATALINA_TMPDIR: /root/tools/tomcat8/temp
    Using JRE_HOME:        /opt/java/jdk/jdk1.8.0_45/jre
    Using CLASSPATH:       /root/tools/tomcat8/bin/bootstrap.jar:/root/tools/tomcat8/bin/tomcat-juli.jar
    Tomcat started.

我们在来一下linux现在所开的端口：

`# netstat –l`

是不是多了个8080端口呢！这个就是Tomcat的默认端口/
好下面开始我们的安装测试了

http://localhost：8080
是不是看到tomcat的Web页面了。

如果还没看到，那就把放火墙关了。

    # service iptables stop

清除防火墙规则：[ 确定 ]
把 chains 设置为 ACCEPT 策略：filter [ 确定 ]
正在卸载 Iiptables 模块：[ 确定 ]，

好了。这次绝对没问题了。

# 5.Tomcat开机自动加载
`# vi /etc/rc.d/rc.local`
//显示如下内容

    #!/bin/sh
    #
    # This script will be executed *after* all the other init scripts.
    # You can put your own initialization stuff in here if you don't
    # want to do the full Sys V style init stuff.

touch /var/lock/subsys/local

//增加下面一句话
`/usr/local/tomcat8/bin/startup.sh`

这样在系统重启后就可以自动启动Tomcat（一定确信JDK安装完并且好用，否则这些都将不好用）

# 6.关闭服务

    # cd /usr/local/tomcat8／bin
    # ./shutdown.sh
    Using CATALINA_BASE: /usr/local/tomcat8
    Using CATALINA_HOME: /usr/local/tomcat8
    Using CATALINA_TMPDIR: /usr/local/tomcat8/temp
    Using JRE_HOME: /usr
    Using CLASSPATH: /usr/local/tomcat８/bin/bootstrap.jar:/usr/local/tomcat８/bin/tomcat-juli.jar

# 7.独立环境的测试

  (1) 测试Java的运行状况,如下命令:

    java -version
    javac -version

看到版本号则表示JDK安装成功

  (2) 测试Apache的运行状况,如下命令:

    /etc/rc.d/init.d/httpd restart ## 重新启动Apache Server

使用任意一浏览器,输入服务器地址后回车,应该可以看到Apache的默认主页

  (3) 测试Jakarta-Tomcat的运行状况,如下命令:
进入tomcat安装目录下面的bin目录

    ./startup.sh ## 启动jakarta-tomcat８服务

使用任意一浏览器,输入服务器地址和端口号8080后回车,应该可以看到jakarta-tomcat８的默认主页
例如: http://localhost:8080

    ./shutdown.sh ## 关闭jakarta-tomcat８服务

# 8.在linux下面安装tomcat后，打开时用户名及密码是什么

在 /usr/local/tomcat８/conf/tomcat-users.xml 文件中。编辑这个文件，可以修改用户名和密码,例如：

    <role name="manager-gui"/>
    <user name="root" password="admin" roles="manager-gui"/>?
    <role rolename="admin-gui"/>
    <user username="admin" password="admin" roles="admin-gui"/>

用户 root 是管理：Status和Tomcat Manager的。
用户 admin 是管理：Host Manager的。
保存后重新启动Tomcat。

# 我在Ubuntu15.4 jdk报Picked up JAVA_TOOL_OPTIONS信息解决

下面是我在网上找了好久找到的：

Ubuntu jdk报Picked up JAVA_TOOL_OPTIONS信息解决

Ubuntu下java一直报下面这句`Picked up JAVA_TOOL_OPTIONS: -javaagent:/usr/share/java/jayatanaag.jar`。 
大概看了下，是ubuntu自带的一个东西。删掉即可。 

`sudo apt-get --purge remove jayatana`

然后我就日了狗了，妈的删了之后都不能编译了

然后通过

`sudo apt-get --purge remove jayatana`

重新安装了这货！

然后google学习了一下linux的环境变量设置。知道每次新建一个shell的时候都会在在～/.bashrc中添加环境变量

所以只需要在该文件中添加

`unset JAVA_TOOL_OPTION`

即可。然后每次进入终端就不会有这个信息提示了。

＃　 关于LINUX权限-bash: ./startup.sh: Permission denied 
在执行./startup.sh,或者./shutdown.sh的时候，爆出了Permission denied，

其实很简单，就是今天在执行tomcat的时候，用户没有权限，而导致无法执行，

用命令chmod 修改一下bin目录下的.sh权限就可以了

如｀chmod u+x *.sh｀

在此执行，OK了。

**参考：**
[http://stackoverflow.com/questions/26074446/picked-up-java-tool-options-javaagent-usr-share-java-jayatanaag-jar-when-st](http://stackoverflow.com/questions/26074446/picked-up-java-tool-options-javaagent-usr-share-java-jayatanaag-jar-when-st)

