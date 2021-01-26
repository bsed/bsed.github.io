---
title: "Ubuntu16.10上安装MongoDB 3.2.10"
categories: [ "SQL" ]
tags: [ "mongodb","ubuntu" ]
draft: false
slug: "ubuntu1610-on-the-installation-of-server-mongodb"
date: "2016-11-12 15:19:00"
---

> 这篇文章主要介绍了Ubuntu系统中安装MongoDB及其启动命令mongod的教程,包括设置MongoDB开机启动的脚本示例.

### 获取最新版本

```bash
wget http://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.2.10.tgz
```

### 解压缩即可执行


<!--more-->


```bash
tar zxvf mongodb-linux-x86_64-3.2.10.tgz
cd /usr/mongodb-linux-x86_64-3.2.10/bin
```

### 方法二：
通过命令安装

```bash
//首先安装mongodb
sudo apt-get install mongodb-server
sudo apt-get install mongodb-client
```
<!--more-->


但是在运行前，需要创建mongodb需要的存放数据和日志的目录：

```bash
sudo mkdir -p /data/db/journal
sudo chmod -R 777 /data/db/
```

### 启动mongodb server

```bash
./mongod -journal -maxConns=2400 -rest
```
- `-journal`  代表要写日志，

- `-maxConns=2400` 代表mongodb 可以接受`2400`个tcp连接，
- `-rest` 代表可以允许客户端通过rest API访问mongdb server.
- `—quiet —logpath /data/db/journal/mongdb.log` 启动可以指定安静模式减少记录的项目数，注意使用该参数必须要同时指定日志路径,比如：

修改系统允许的最大连接数

上面的最大连接数目的限制原因是Linux系统默认一个进程最大文件打开数目为1024，用 `ulimit -a` 命令检查，可以看到下面这行：

```bash
open files           (-n) 1024
```

修改 `/etc/security/limits.conf` 配置文件。
使用命令：`sudo gedit /etc/security/limits.conf`
在文件中增加
```bash
* soft nofile 3000
* hard nofile 20000
root soft nofile 3000
root hard nofile 20000
```
* 表示该配置对所有用户均有效,root用户要特别加两行。
  硬限制通常是根据系统硬件资源状况(主要是系统内存)计算出来的系统最多可同时打开的文件数量，软限制是在这个基础上进一步的限制。因此软限制数目要低于硬限制。
  nofile表示 max number of open files
  重新启动计算机，然后再用ulimit -a 命令查看：
```bash
open files           (-n) 3000
```
已经生效了。现在再启动mongodb server，问题解决

### 设置开机启动
在`/etc/init.d/`目录下新建脚本文件`mongodb`

```bash
#!/bin/sh 
   
### BEGIN INIT INFO 
# Provides:   mongodb 
# Required-Start: 
# Required-Stop: 
# Default-Start:    2 3 4 5 
# Default-Stop:     0 1 6 
# Short-Description: mongodb 
# Description: mongo db server 
### END INIT INFO 
   
. /lib/lsb/init-functions
   
PROGRAM=/usr/mongodb-linux-x86_64-3.2.10/bin/mongod
MONGOPID=`ps -ef | grep 'mongod' | grep -v grep | awk '{print $2}'` 
   
test -x $PROGRAM || exit 0 
   
case "$1" in
 start) 
   ulimit -n 3000 
   log_begin_msg "Starting MongoDB server" 
   $PROGRAM --fork --quiet -journal -maxConns=2400 -rest --logpath /data/db/journal/mongdb.log 
   log_end_msg 0 
   ;; 
 stop) 
   log_begin_msg "Stopping MongoDB server" 
   if [ ! -z "$MONGOPID" ]; then 
    kill -15 $MONGOPID 
   fi
   log_end_msg 0 
   ;; 
 status) 
   ;; 
 *) 
   log_success_msg "Usage: /etc/init.d/mongodb {start|stop|status}" 
   exit 1 
esac 
   
exit 0 
```
请注意，用 `sudo chmod +x /etc/init.d/mongodb` 命令允许该脚本可被执行
接着运行下面的命令注册开机脚本：
```bash
update-rc.d mongodb defaults
 Adding system startup for /etc/init.d/mongodb ...
  /etc/rc0.d/K20mongodb -> ../init.d/mongodb
  /etc/rc1.d/K20mongodb -> ../init.d/mongodb
  /etc/rc6.d/K20mongodb -> ../init.d/mongodb
  /etc/rc2.d/S20mongodb -> ../init.d/mongodb
  /etc/rc3.d/S20mongodb -> ../init.d/mongodb
  /etc/rc4.d/S20mongodb -> ../init.d/mongodb
  /etc/rc5.d/S20mongodb -> ../init.d/mongodb
```
你也可以通过 `update-rc.d -f mongodb remove` 删除
重新启动，通过 `ps -def | grep mongod` 可以查看到自启动的服务进程，然后可以通过下面的命令`关闭/启动`服务
```bash
sudo service mongodb stop
sudo service mongodb start
```
## 客户端登录服务器

启动日志如上，服务端启动如上，现在我们在另外一个终端测试服务器是否正常。
进入`/usr/local/mongodb-linux-x86_64-3.2.10/bin`,执行./mongo
出现

```bash
MongoDB shell version: 3.2.10
connecting to: test
```
执行

```bash
db.foo.save({1 : “Hello world”})
```
然后查找
```
db.foo.find();
```
看到
```
{ "_id" : ObjectId("7d4b397986738efa2d0718gh"), "1" : "hello world" }
```

执行到这里恭喜你，成功安装好了`mongodb`

也可以通过下面这种方式连接远程的mongodb server,默认端口为27017,比如
`./mongo 192.168.0.1`

### 创建数据库

如果没有mydb数据库的话，在客户端中使用命令：

```sql
use mydb
```
将创建mydb数据库，而且当前数据库切换为mydb.
此时show dbs不显示该数据库名称。使用db.stats()命令检查当前数据库状态。

标准检查流程

1. 首先检查 `ulimit -a`
查看`open files (-n)` 是否为设置的值

2. 查看该服务是否启动
```bash
ps -def | grep mongod
```

3. 查看服务器是否正确
```bash
cd /data/db/journal/
cat mongdb.log
```
4. 看服务器是否启动正常
进入`http://192.168.0.1:28017`

5. 进入`/usr/mongodb-linux-x86_64-3.2.10/bin`,执行 `./mongo`

看看是否能够登录

## 安裝PHP MongoDB擴展

```bash
sudo apt-get install php5-dev php5-cli php-pear
sudo pecl install mongo
```
在`php.ini`中加入

```bash
extension=mongo.so
```
**Mongodb启动命令mongod参数说明**

mongod的主要参数有：

- --quiet # 安静输出

- --port arg # 指定服务端口号，默认端口27017

- --bind_ip arg # 绑定服务IP，若绑定127.0.0.1，则只能本机访问，不指定默认本地所有IP

- --logpath arg # 指定MongoDB日志文件，注意是指定文件不是目录

- --logappend # 使用追加的方式写日志

- --pidfilepath arg # PID File 的完整路径，如果没有设置，则没有PID文件

- --keyFile arg # 集群的私钥的完整路径，只对于Replica Set 架构有效

- --unixSocketPrefix arg # UNIX域套接字替代目录,(默认为 /tmp)

- --fork # 以守护进程的方式运行MongoDB，创建服务器进程

- --auth # 启用验证

- --cpu # 定期显示CPU的CPU利用率和iowait

- --dbpath arg # 指定数据库路径

- --diaglog arg # diaglog选项 0=off 1=W 2=R 3=both 7=W+some reads

- --directoryperdb # 设置每个数据库将被保存在一个单独的目录

- --journal # 启用日志选项，MongoDB的数据操作将会写入到journal文件夹的文件里

- --journalOptions arg # 启用日志诊断选项

- --ipv6 # 启用IPv6选项

- --jsonp # 允许JSONP形式通过HTTP访问（有安全影响）

- --maxConns arg # 最大同时连接数 默认2000

- --noauth # 不启用验证

- --nohttpinterface # 关闭http接口，默认关闭27018端口访问

- --noprealloc # 禁用数据文件预分配(往往影响性能)

- --noscripting # 禁用脚本引擎

- --notablescan # 不允许表扫描

- --nounixsocket # 禁用Unix套接字监听

- --nssize arg (=16) # 设置信数据库.ns文件大小(MB)

- --objcheck # 在收到客户数据,检查的有效性，

- --profile arg # 档案参数 0=off 1=slow, 2=all

- --quota # 限制每个数据库的文件数，设置默认为8

- --quotaFiles arg # number of files allower per db, requires --quota

- --rest # 开启简单的rest API

- --repair # 修复所有数据库run repair on all dbs

- --repairpath arg # 修复库生成的文件的目录,默认为目录名称dbpath

- --slowms arg (=100) # value of slow for profile and console log

- --smallfiles # 使用较小的默认文件

- --syncdelay arg (=60) # 数据写入磁盘的时间秒数(0=never,不推荐)

- --sysinfo # 打印一些诊断系统信息

- --upgrade # 如果需要升级数据库  * Replicaton 参数

- --fastsync # 从一个dbpath里启用从库复制服务，该dbpath的数据库是主库的快照，可用于快速启用同步

- --autoresync # 如果从库与主库同步数据差得多，自动重新同步，

- --oplogSize arg # 设置oplog的大小(MB)  * 主/从参数

- --master # 主库模式

- --slave # 从库模式

- --source arg # 从库 端口号

- --only arg # 指定单一的数据库复制

- --slavedelay arg # 设置从库同步主库的延迟时间  * Replica set(副本集)选项：

- --replSet arg # 设置副本集名称  * Sharding(分片)选项

- --configsvr # 声明这是一个集群的config服务,默认端口27019，默认目录/data/configdb

- --shardsvr # 声明这是一个集群的分片,默认端口27018

- --noMoveParanoia # 关闭偏执为moveChunk数据保存

  ​

  上述参数都可以写入`mongod.conf` 配置文档里例如：

```bash
dbpath = /data/mongodb
logpath = /data/mongodb/mongodb.log
logappend = true
port = 27017
fork = true
auth = true
```

e.g：

```bash
./mongod -shardsvr -replSet sharing -port 17171 -dbpath /data/mongodb/data/sharing -oplogSize 100 -logpath /data/mongodb/logs/sharing.log -logappend -fork -rest
```

## 搭建mongodb的简单服务

新建一个文件夹`mongod_conf` 中包含以下四个文件 `bin`，`conf`，`data`，`log`

在文件`conf` 文件夹下新建一个`mongod.conf` 文件
```
port = 17071        //端口号
depath = data        //数据存贮的目录
logpath = log/mongod.log   //日志文件的路径  需要指明实际的文件
fork =true       //后台进程
```
进行上层目录 

```bash
cd ..
/usr/bin/mongo -f conf/mongod.conf
/usr/bin/mongo 127.0.0.1:17071/test
```
如果出现了向右的箭头标记 表示启动成功

关闭mongod服务两种方法
方法一：

```
use admin
db.shutdownServer();
```
方法二：
```bash
kill -15
```
### 重新启动Mongod
此处需要先安装 `sudo apt-get install numactl`
```bash
numactl --interleave=all /usr/bin/mongod -f conf/mongod.conf
```
启动之后我们可以查看日志
mongd在17071等待连接

##mongodb 基本使用

```bash
/usr/bin/mongo 127.0.0.1:17071
show dbs;// 查看当前系统有多少数据库
use demo    //使用use切换数据库
db.dropDatabase();//删除数据库
use demo //此处无需创建数据库，MONGOd会在需要的时候自己创建数据库
db.demo_data.insert({x:1}) //写入的数据为JSON数据
show dbs //能看到demo数据库重新创建了
```
### 查询数据库
```
db.demo_data.find()  //_id 全局唯一的字段mongod自己创建的
db.demo_data.insert({x:2,_id:1}) //_id可以自己指定
```
find 支持limit,skip(跳过)数据

### 插入多条数据，使用语法
```
for(i=3;i<100;i++)db.demo_data.insert({x:i})  // 插入了97条数据
db.demo_data.find().cout()//算出有多少条数据
```
mongodb支持连续操作 db.demo_data.find().skip(3),limit(2).sort({x:1})

### 数据的更新
查找的记录的条件 和更新的数据
```
db.demo_data.update({x:1},{x:999})
```
此处有两个参数
第一个参数表示查找的地方，第二个表示要改的地方
```
db.demo_data.insert({x:100,y:100,z:100})
db.demo_data.update({z:100},{$set:{y:99}}) // 此处表示若更改则会把X,Z的值覆盖 使用set能够避免覆盖
更改一条不存在的数据的时候 默认插入到数据库中
db.demo_data.update({y:100},{y:999},true)  //此处增加TRUE 表示增加一天不存在的数据
//update更新多条数据
db.demo_data.insert({c:1})
db.demo_data.insert({c:1})
db.demo_data.insert({c:1})
db.demo_data.update({c:1},{$set:{c:2}},false,true)
//删除数据
db.demo_data.remove({x:3})
//删除数据表
db.demo_data.drop()
//查看索引
db.demo_data.getIndexes()
//开启索引
db.demo_data.ensureIndex({x:1})   x= 1表示正向排序，x=-1表示逆向排序
```

参考：

 - [http://www.linuxidc.com/Linux/2014-04/100753.htm](http://www.linuxidc.com/Linux/2014-04/100753.htm)