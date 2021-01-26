---
title: "Sqoop启动报错(Please set $HADOOP_COMMON_HOME to the root)"
categories: [ "大数据/NLP" ]
tags: [ "hadoop","sqoop" ]
draft: false
slug: "sqoop-start-error-set-hadoopcommonhome-the-to-please-root"
date: "2016-11-27 16:17:00"
---

> [root@bigdata ~]# sqoop version Error:
> /opt/sqoop-1.4.6/bin/../../hadoop does not exist! Please set
> $HADOOP_COMMON_HOME to the root of your Hadoop installation.

需要配置`sqoop-env.sh`文件

在sqoop中conf目录下新复制一个sqoop-env.sh文件：


<!--more-->


```bash
[root@bigdata conf]# cp sqoop-env-template.sh sqoop-env.sh
```
修改配置`sqoop-env.sh`文件：
```bash
export HADOOP_COMMON_HOME=/opt/hadoop-2.7.3
#Set path to where hadoop-*-core.jar is available
export HADOOP_MAPRED_HOME=/opt/hadoop-2.7.3
#set the path to where bin/hbase is available
#export HBASE_HOME=
#Set the path to where bin/hive is available
#export HIVE_HOME=
#Set the path for where zookeper config dir is
#export ZOOCFGDIR=
```