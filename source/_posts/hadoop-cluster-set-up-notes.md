---
title: Hadoop集群搭建笔记
date: 2016-11-20 13:41:00
updated: 2016-11-21 13:43:38
tags: 
- linux
- axel
categories: 
- linux

---
## 1、安装虚拟机

 - VirtualBox-5.1.6
 - CentOS-6.8-x86_64-minimal.iso
 - Ubuntu16.10
 - hadoop-2.7.3.tar.gz
 
安装Linux虚拟机：（在ubuntu16.10上）

1）安装VirtualBox（可修改配置）

2）添加CentOS镜像（启动安装 ->配置网络）

网络配置：NAT模式(桥接模式)

网络重启：`service network restart`

关闭系统：`shutdown -h now`


<!--more-->


## 2、远程连接

 - ssh
 - filezilla

 
## 3、在Linux上搭建Hadoop集群

下载软件

 1. [Jdk：http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html](Jdk：http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
 2. [Hadoop包:http://apache.fayea.com/hadoop/common/hadoop-2.7.3/](Hadoop包:http://apache.fayea.com/hadoop/common/hadoop-2.7.3/)

步骤：

1、修改`/etc/hosts`

2、配置JDK

安装：rpm -ivh jdk-8u111-linux-x64.rpm
```bash
vi /etc/profile
JAVA_HOME
source /etc/profile
java -version
```
## 3、配置SSH（免密码登录）
```
ssh-keygen -t rsa
cat xxx.pub >> authorized_keys
chmod 644 authorized_keys
ssh IP地址/HOSTNAME（主机名）
```
ssh-keygen -t rsa 直接按回车就可以了

## 4、安装及配置Hadoop
我的 hadoop 准备安装到 /opt 目录下
```
tar zxf hadoop-2.7.3.tar.gz
cd /opt/hadoop-2.7.3/etc/hadoop/
```

**core-site.xml**

configure下 添加如下内容

```xml
<property>
    <name>fs.default.name</name>
    <value>hdfs://bigdata:9000</value>
  </property>
 
 <property>
    <name>hadoop.tmp.dir</name>
    <value>/opt/hadoop-2.7.3/current/tmp</value>
  </property>
 <property>
    <name>fs.trash.interval</name>
    <value>4320</value>
  </property>
```

**hdfs-site.xml**
configure下 添加如下内容
```xml
<property>
   <name>dfs.namenode.name.dir</name>
   <value>/opt/hadoop-2.7.3/current/dfs/name</value>
 </property>
 <property>
   <name>dfs.datanode.data.dir</name>
   <value>/opt/hadoop-2.7.3/current/data</value>
 </property>
 <property>
   <name>dfs.replication</name>
   <value>1</value>
 </property>
 <property>
   <name>dfs.webhdfs.enabled</name>
   <value>true</value>
 </property>
 <property>
   <name>dfs.permissions.superusergroup</name>
   <value>staff</value>
 </property>
 <property>
   <name>dfs.permissions.enabled</name>
   <value>false</value>
 </property>
```

**yarn-site.xml**
configure下 添加如下内容

```xml
<property>
   <name>yarn.resourcemanager.hostname</name>
   <value>bigdata</value>
 </property>
 <property>
   <name>yarn.nodemanager.aux-services</name>
   <value>mapreduce_shuffle</value>
 </property>
 <property>
   <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
   <value>org.apache.hadoop.mapred.ShuffleHandler</value>
 </property>
 <property>
   <name>yarn.resourcemanager.address</name>
   <value>bigdata:18040</value>
 </property>
<property>
   <name>yarn.resourcemanager.scheduler.address</name>
   <value>bigdata:18030</value>
 </property>
 <property>
   <name>yarn.resourcemanager.resource-tracker.address</name>
   <value>bigdata:18025</value>
 </property> <property>
   <name>yarn.resource.manager.admin.address</name>
   <value>bigdata:18141</value>
 </property>
<property>
   <name>yarn.resourcemanager.webapp.address</name>
   <value>bigdata:18088</value>
 </property>
<property>
   <name>yarn.log-aggregation-enable</name>
   <value>true</value>
 </property>
<property>
   <name>yarn.log-aggregation.retain-seconds</name>
   <value>86400</value>
 </property>
<property>
   <name>yarn.log-aggregation.retain-check-interval-seconds</name>
   <value>86400</value>
 </property>
<property>
   <name>yarn.nodemanager.remote-app-log-dir</name>
   <value>/tmp/logs</value>
 </property>
<property>
   <name>yarn.nodemanager.remote-app-log-dir-suffix</name>
   <value>logs</value>
 </property>
```

**mapred-site.xml**

configure下 添加如下内容
```xml
<property>
  <name>mapreduce.framework.name</name>
  <value>yarn</value>
</property>
<property>
  <name>mapreduce.jobtracker.http.address</name>
  <value>bigdata:50030</value>
</property>
<property>
  <name>mapreduce.jobhisotry.address</name>
  <value>bigdata:10020</value>
</property>
<property>
  <name>mapreduce.jobhistory.webapp.address</name>
  <value>bigdata:19888</value>
</property>
<property>
  <name>mapreduce.jobhistory.done-dir</name>
  <value>/jobhistory/done</value>
</property>
<property>
  <name>mapreduce.intermediate-done-dir</name>
  <value>/jobhisotry/done_intermediate</value>
</property>
<property>
  <name>mapreduce.job.ubertask.enable</name>
  <value>true</value>
</property>
```

编辑 slaves 文件
内容改为 centos虚拟机的计算机名
```
bigdata
```
 
修改 `hadoop-env.sh` JAVA_HOME 为下面内容

```bash
export JAVA_HOME=/usr/java/default/
```

## 5、格式化HDFS

```
hdfs namenode -format
```
主要看如下内容是否正确。

> ‘16/09/0403:07:30 INFO common.Storage: Storage directory
> /opt/hadoop-2.7.3/current/dfs/namehas been successfully formatted.’

 
## 6、启动Hadoop集群

```bash
/opt/hadoop-2.7.3/sbin/start-all.sh
```
 
7、验证Hadoop集群
bash 下执行如下命令：
```bash
jps
```
会看到 如下内容 说明hadoop 集群部署成功。
```
[root@bigdata ~]# jps
5014 SecondaryNameNode
4856 DataNode
4731 NameNode
5164 ResourceManager
5260 NodeManager
```

关闭防火墙 或者 在防火墙的规则中开放这些端口
```bash
service iptables stop
```
访问：
hdfs:  [http://bigdata:50070](http://bigdata:50070)

yarn:  [http://bigdata:18088](http://bigdata:18088)

如图：
![hadoop_dfs.png][1]
![hadoop_cluster.png][2]
## 8. 关机集群

```bash
/opt/hadoop-2.7.3/sbin/stop-all.sh
```


  [1]: https://imgs.gnux.cn/usr/uploads/2016/11/401888403.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/11/1461674002.png