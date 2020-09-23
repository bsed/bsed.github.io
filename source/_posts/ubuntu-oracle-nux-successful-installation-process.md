---
title: *nux ubuntu oracle 成功安装过程
date: 2010-10-04 20:22:00
updated: 2016-01-24 08:49:34
tags: 
- rgba
- rgb
categories: 
- js

---
因为工作需要，在本机的ubuntu10.10装了一个oracle 10g，个人觉得学一门东西，应当从安装开始，比较linux,mysql,oracle。费话不多说，下面来说一下我的安装过程。
## 下载一个oracle的基本包
其实我本想装oracle 11g，但是网速不给力，找同事要了一个oracle 10g。文件名10201_database_linux32.zip，不知道有没有改过。这个可以到官方网站去下。

## 安装oracle 10g所要的包

    apt-get install gcc make binutils lesstif2 libc6 libc6-dev rpm libmotif3 libaio1 libstdc++6 alien

## 创建oracle用户

    sudo adduser oracle  


<!--more-->


## 查看，设置交换分区的大小

    free -m  
    dd if=/dev/zero of=swaptmp bs=9024 count=100000  
    mkswap swaptmp  
    swapon swaptmp  
    free -m                //这个时候就加上了  

用free -m查看一下交换分区swap的大小。我在网上看了一下，有人说内存和swap的比例1:2比较好，也有人说1:1比较好。我用的是1:2，整个oracle安装完，差不多10分钟。3.5G内存，4核CPU，这样的配置非常给力的。哈哈。
注意：这个swaptmp只是临时的，如果重起，用free -m又会回到从前。
安装完不想要swaptmp可以删除掉。

    swapoff swaptmp  
    rm swaptmp  

## 修改 /etc/sysctl.conf

    kernel.shmmax = 3147483648  
    kernel.shmmni = 4096  
    kernel.shmall = 2097152  
    kernel.sem = 250 32000 100 128  
    fs.file-max = 65536  
    net.ipv4.ip_local_port_range = 1024 65000  

在结尾添加上面的内容

## 修改/etc/security/limits.conf

    * soft nproc 2407  
    * hard nproc 16384  
    * soft nofile 1024  
    * hard nofile 65536

  
在结尾添加上面的内容，sudo sysctl -p让参数生效

## 产生一些软链接

    sudo ln -s /usr/bin/awk /bin/awk  
    sudo ln -s /usr/bin/rpm /bin/rpm  
    sudo ln -s /usr/bin/basename /bin/basename  
    sudo mkdir /etc/rc.d  
    sudo ln -s /etc/rc0.d /etc/rc.d/rc0.d  
    sudo ln -s /etc/rc2.d /etc/rc.d/rc2.d  
    sudo ln -s /etc/rc3.d /etc/rc.d/rc3.d  
    sudo ln -s /etc/rc4.d /etc/rc.d/rc4.d  
    sudo ln -s /etc/rc5.d /etc/rc.d/rc5.d  
    sudo ln -s /etc/rc6.d /etc/rc.d/rc6.d  
    sudo ln -s /etc/init.d /etc/rc.d/init.d  

## 创建版本声明,新建/etc/redhat-release

    Red Hat Linux release 3.1 (drupal)  

结尾加上上面的内容。以使安装程序认为正在一个hedhat的系统上安装。这一点，不太明白，难道ubuntu得罪了oracle，非要冒充redhat.

## 修改环境变量

    export ORACLE_HOME=/opt/ora10  
    export ORACLE_OWNER=oracle  
    export ORACLE_SID=ora1  
    export ORACLE_TERM=xterm  
    export PATH=$ORACLE_HOME/bin:$ORACLE_HOME/Apache/Apache/bin:$PATH  

编辑 /home/oracle/.bashrc在结尾加上上面的内容。

## 解压10201_database_linux32.zip，并且重新启动用oracle用户登录

    unzip 10201_database_linux32.zip  
    sudo reboot  
    cd database  
    ./runInstaller  

这样就可以进入图形界面安装了。但是会遇到一些问题。

## 遇到的问题，解决，以及要注意的地方

### 乱码问题

    export LC_ALL=C  

英文界面安装

oracle 安装选择字符集
### 选择字符集

### /opt/ora10的权限问题 

先查看一下，/opt目录下有没有ora10这个目录，开始我没有建，我以为安装的时候，oracle会自动建。其实不是的。

    sudo mkdir /opt/ora10  
    sudo chown -R oracle:oracle /opt/ora10  

### 安装libstdc++5
`sudo apt-get install libstdc++5`，安装的libstdc++6过新了，然后在点上图中的retry
### 添加oracle的一些管理密码
## 安装到倒数第二步，会弹出个对话框，要求你执行二个shell脚本，不要急着点next

    sudo sh /home/oracle/oraInventory/orainstRoot.sh  
    sudo sh /opt/ora10/root.sh  

报错：/bin/chgrp: 无效的组：“nobody”，

    sudo addgroup nobody  
    sudo usermod -g nobody nobody  

## 记录下一些管理网址
oracle 安装成功
oracle 安装成功
上面是一些后台管理的url
## 启动脚本

    #!/bin/bash  
    #  
    # /etc/init.d/oracledb  
    #  
    # Run-level Startup script for the Oracle Instance, Listener, and  
    # Web Interface  
      
    export ORACLE_HOME=/opt/ora10  
    export ORACLE_SID=ora1  
    export PATH=$ORACLE_HOME/bin:$ORACLE_HOME/Apache/Apache/bin:$PATH  
      
    ORA_OWNR="oracle"  
    # if the executables do not exist -- display error  
    if [ ! -f $ORACLE_HOME/bin/dbstart -o ! -d $ORACLE_HOME ]  
    then  
    echo "[Oracle Error] dbstart command does not exist, quitting..."  
    exit 1  
    else  
    echo "[Oracle Info] dbstart existance check OK"  
    fi  
      
    # depending on parameter -- startup, shutdown, restart  
    # of the instance and listener or usage display  
    case "$1" in  
    start)  
    # Oracle listener and instance startup  
    echo "[Oracle Info] Starting oracle"  
    echo "[Oracle Info] su $ORA_OWNR -c ""$ORACLE_HOME/bin/lsnrctl start"  
    su $ORA_OWNR -c "$ORACLE_HOME/bin/lsnrctl start"  
    if [ $? -ne 0 ]  
    then  
     echo "[Oracle Error] listener failed to start"  
    exit 1  
    else  
     echo "[Oracle Info] listener start OK"  
    fi  
      
    su $ORA_OWNR -c "$ORACLE_HOME/bin/dbstart $ORACLE_HOME"  
    echo "[Oracel Info] su $ORA_OWNR -c ""$ORACLE_HOME/bin/dbstart $ORACLE_HOME"  
    if [ $? -ne 0 ]  
    then  
     echo "[Oracle Error] dbstart failed to start"  
    exit 1  
    else  
     echo "[Oracle Info] dbstart OK"  
    fi  
    touch /var/lock/oracle  
    echo "[Oracle Info]su $ORA_OWNR -c ""$ORACLE_HOME/bin/emctl start dbconsole"  
    su $ORA_OWNR -c "$ORACLE_HOME/bin/emctl start dbconsole"  
    if [ $? -ne 0 ]  
    then  
     echo "[Oracle Error] dbconsole failed to start"  
    exit 1  
    else  
     echo "[Oracle Info] dbconsole start OK"  
    fi  
    echo "[Oracle Info] Starting oracle ended OK"  
    ;;  
    stop)  
    # Oracle listener and instance shutdown  
    echo "[Oracle Info] Shutdown Oracle: "  
    echo "[Oracle Info] su $ORA_OWNR -c ""$ORACLE_HOME/bin/lsnrctl stop"  
    su $ORA_OWNR -c "$ORACLE_HOME/bin/lsnrctl stop"  
    echo "[Oracle Info] su $ORA_OWNR -c ""$ORACLE_HOME/bin/dbshut $ORACLE_HOME"  
    su $ORA_OWNR -c "$ORACLE_HOME/bin/dbshut $ORACLE_HOME"  
    rm -f /var/lock/oracle  
    echo "[Oracle Info]su $ORA_OWNR -c ""$ORACLE_HOME/bin/emctl stop dbconsole"  
    su $ORA_OWNR -c "$ORACLE_HOME/bin/emctl stop dbconsole"  
    echo "[Oracle Info] Shutdown Oracle ended OK"  
    ;;  
    reload|restart)  
    echo "[Oracle Info]: $1 oracle"  
    $0 stop  
    $0 start  
    ;;  
    *)  
    echo "[Oracle Error] Unknown context: $1"  
    echo "[Oracle Info]  Usage: `basename $0` start|stop|restart|reload"  
    exit 1  
    esac  
    exit 0  

从网上找的启动脚本，还要设置一下脚本权限
sudo chmod 755 /etc/init.d/oracledb  
sudo update-rc.d oracledb defaults 99  
0
