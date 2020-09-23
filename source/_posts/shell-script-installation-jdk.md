---
title: shell脚本安装JDK
date: 2013-11-03 08:24:00
updated: 2015-11-07 08:25:38
tags: 
- read
- book
categories: 
- default

---
shell脚本安装JDK

这个脚本我一直在用但不是我写的，可以在centos和ubuntu下使用，其它系统没测试，使用时要先去官网下好jdk,并改好脚本里的文件名和安装目录


<!--more-->


    #!/bin/bash
    chmod u+x jdk-6u33-linux-x64.bin
    installPath=/usr/java
    if [ ! -d /usr/java ];
     then
         mkdir /usr/java
     fi
    ./jdk-6u33-linux-x64.bin -D  /usr/java
    cp  -R jdk1.6.0_33/ /usr/java/jdk1.6.0_33/
    touch environment
    echo "PATH=\"$PATH:/usr/java/jdk1.6.0_33/bin\"" >> environment
    echo "JAVA_HOME=/usr/java/jdk1.6.0_33" >> environment
    echo "CLASSPATH=.:%JAVA_HOME%/lib/dt.jar:%JAVA_HOME%/lib/tools.jar" >> environment
    rm -rf /etc/environment
    cp environment /etc
    source /etc/environment
    update-alternatives --install /usr/bin/java java /usr/java/jdk1.6.0_33/bin/java 300
    update-alternatives --install /usr/bin/javac javac /usr/java/jdk1.6.0_33/bin/javac 300
    update-alternatives --config java

