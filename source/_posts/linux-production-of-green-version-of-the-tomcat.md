---
title: linux下制作绿色版的tomcat
date: 2014-04-21 09:20:00
updated: 2015-11-07 09:28:23
tags: 
- javascript
- ecmascript6
categories: 
- js

---
## 一、为什么要绿色化？

方便批量运维，不用每台服务器都配置JDK/tomcat/APR
各组件独立，方便升级JDK/tomcat/apr
方便调整JVM参数和tomcat参数

前提：
服务器是linux 64位系统，发行版可以不一致
总之,要实现的目的是：将绿色化后的包传到任一服务器上，不用任何配置就能使用，同时又方便管理和升级

## 二、需要独立的模块

1. JDK : 不用配置系統环境变量
2. tomcat : 其中conf和logs目录需独立出来，确保升级tomcat会保留原配置和日志
3. APR : tomcat使用的apr不需要重新编译
4. 独立的启动/停止脚本统一控制


<!--more-->


制作好的目录结构：

    /opt/working/
    ├── apr/
    ├── conf/
    ├── jdk/
    ├── logs/
    ├── start.sh
    ├── stop.sh
    └── tomcat/

## 三、制作流程

### 1. JDK

创建目录：

    mkdir  /opt/working/jdk

去oracle官方下载好linux 64位的JDK，解压到/opt/working/jdk即可。
如果觉得JDK包太大，也可以用jre，但是最后面的控制脚本要改一下。

### 2. 配置tomcat

tomcat只需将conf目录和logs目录独立出来即可，这样做目的是升级tomcat时不会覆盖原有的配置和日志。
具体作法：将tomcat的conf和logs目录移动到working目录，并在tomcat目录下创建软链接。

    cd tomcat && mv conf logs ../ && ln -s ../conf . && ln -s ../logs .

### 3. apr

在woring目录下创建apr目录：

    mkdir  /opt/working/apr/ 

下载apr和apr-util并解压:

    cd /tmp
    curl http://www.motorlogy.com/apache//apr/apr-1.5.2.tar.gz |tar xvz
    curl http://www.motorlogy.com/apache//apr/apr-util-1.5.4.tar.gz |tar xvz

先编译apr在编译apr-util:

apr:

    cd apr-1.5.2  &&  ./configure &&   make && make install

apr-util:

    cd ../apr-util-1.5.4/ && ./configure --with-apr=/usr/local/apr/
    make && make install

编译tomcat-native.tar.gz ：

    cd /opt/working/tomcat/bin &&   tar -xvf tomcat-native.tar.gz 
    cd tomcat-native-1.1.33-src/jni/native/
    ./configure --with-apr=/usr/local/apr/ --with-java-home=/opt/working/jdk/

将编译好的apr移动到/opt/working/apr

    mv /usr/local/apr /opt/working/apr

详细配置可以参考：

[https://yigrherb.com/2013/12/23/tomcat-using-the-apr-model.html](https://yigrherb.com/2013/12/23/tomcat-using-the-apr-model.html)

### 4. 脚本

使用脚本来控制tomcat启动所需的环境变量（jdk/apr）、jvm参数（CATALINA_OPTS）和系统配置（清临时目录/ulimit参数/权限等）

附上启动脚本和关闭脚本：

*start.sh:*

    #!/bin/bash
    
    TOMCAT_HOME=`pwd`/tomcat
    TOMCAT_HOME=$TOMCAT_HOME
    cd $TOMCAT_HOME
    
    if [ ! -L conf ] || [ ! -L logs ]
    then
      rm -rf conf logs && ln -s ../conf . && ln -s ../logs .
    fi
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$TOMCAT_HOME/../apr/lib
    export JAVA_HOME=$TOMCAT_HOME/../jdk
    export CATALINA_HOME=$TOMCAT_HOME
    # for large memory
    export CATALINA_OPTS="-server -Xss256k -Xms3500m -Xmx3500m -XX:NewRatio=2 -XX:SurvivorRatio=2 -XX:MaxPermSize=300m -XX:NewSize=400m -XX:+UseConcMarkSweepGC -verbose:gc -Dfile.encoding=UTF-8 -Ddefault.client.encoding=UTF-8 -Duser.language=zh -Duser.region=CN"
    
    # for small memory
    #export CATALINA_OPTS="-server -Xss256k -Xms300m -Xmx300m -XX:MaxPermSize=100m -XX:NewRatio=2 -XX:SurvivorRatio=2 -XX:NewSize=40m -XX:+UseConcMarkSweepGC -verbose:gc -XX:+PrintGCDetails  -XX:+PrintGCTimeStamps -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime -Dfile.encoding=UTF-8 -Ddefault.client.encoding=UTF-8 -Duser.language=zh -Duser.region=CN"
    
    ulimit -n 36000
    rm -rf $CATALINA_HOME/work/Catalina/*
    chmod +x $CATALINA_HOME/bin/*.sh
    chmod +x $JAVA_HOME/bin/*
    $CATALINA_HOME/bin/startup.sh &
    
    #tail -f $CATALINA_HOME/logs/catalina.out|sed '/^[DEBUG|INFO]/d'

*stop.sh:*

    #!/bin/bash
    TOMCAT_HOME=`pwd`/tomcat
    TOMCAT_HOME=$TOMCAT_HOME
    cd $TOMCAT_HOME
    
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$TOMCAT_HOME/../apr/lib
    export JAVA_HOME=$TOMCAT_HOME/../jdk
    export CATALINA_HOME=$TOMCAT_HOME
    export CATALINA_OPTS="-server "
    $CATALINA_HOME/bin/shutdown.sh
    sleep 3
    pkill -9 -f  $JAVA_HOME/bin/java
    rm -rf $TOMCAT_HOME/work/Catalina/*
    rm -rf $TOMCAT_HOME/conf/Catalina/*



