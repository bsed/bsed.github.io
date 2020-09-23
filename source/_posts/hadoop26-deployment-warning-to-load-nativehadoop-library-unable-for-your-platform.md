---
title: hadoop2.7部署警告： Unable to load native-hadoop library for your platform
date: 2015-08-24 15:57:00
updated: 2016-11-19 16:05:27
tags: 
- linux
- Backup
categories: 
- java

---
刚装好Hadoop的时候，每次输入命令运行都会出现：

>WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform… using builtin-Java classes where applicable

提示hadoop不能加载本地库。

以前官网会提供32位的安装包，但目前提供的下载包为64位的，在Linux 32位系统下安装后会一直提示错误“WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform… using builtin-Java classes where applicable ”，但官网又不提供32位系统下的安装包，所以我们只能自己编译打包源码。
虽然我在64位的linux上重新编译了hadoop，并跟踪编译后端hadoop2.7进行部署。部署后启动，但出现了警告：
WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable

**方法一：**

该警告通过如下方法消除了：
在`hadoop-env.sh`中 修改`HADOOP_OPTS`：
```bash
exportHADOOP_OPTS="-Djava.library.path=$HADOOP_PREFIX/lib:$HADOOP_PREFIX/lib/native"
```
**方法二：**
在`/etc/profile`中，添加下面配置：
```
export  HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export  HADOOP_HOME=/home/hadoop/labc/hadoop-2.7.1
export  HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib:$HADOOP_COMMON_LIB_NATIVE_DIR"
最后记得使配置生效：source /etc/profile
```
并把相同配置添加到`hadoop-env.sh文件末尾。

输入如下命令，没有报warn，说明修改成功。

**参考**:

 - [http://www.powerxing.com/install-hadoop/](http://www.powerxing.com/install-hadoop/)
 - [http://blog.csdn.net/young_kim1/article/details/50324345](http://blog.csdn.net/young_kim1/article/details/50324345)