---
title: Redis数据库常用数据结构学习
date: 2018-05-22 16:29:00
updated: 2018-12-27 08:54:19
tags: 
- git
categories: 
- linux

---
## 1、Redis简介

**Remote Dictionary Server(Redis)**是一个由**Salvatore Sanfilippo**写的**key-value**存储系统。Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供**多种语言的API**。它通常被称为数据结构服务器，因为值（value）可以是 字符串(String), 哈希(Map), 列表(list), 集合(sets) 和 有序集合(sorted sets)等类型。
一个事务经历三个阶段
大家都知道了redis是基于**key-value**的**no sql数据库**，因此，先来了解一下关于key相关的知识点
> 1、任何二进制的序列都可以作为key使用 
> 2、Redis有统一的规则来设计key 
> 3、对key-value允许的最大长度是512MB


<!--more-->


## 2、支持的语言
```bash
ActionScript Bash  C  C#  C++  Clojure Common Lisp
Crystal  D  Dart  Elixir  emacs  lisp  Erlang  
Fancy  gawk  GNU Prolog  Go  Haskell  Haxe  Io Java  Javascript  
Julia  Lua  Matlab  mruby  Nim  Node.js  Objective-C  
OCaml Pascal  Perl  PHP  Pure Data  Python  R  Racket  
Rebol  Ruby  Rust Scala  Scheme  Smalltalk  Swift  Tcl  VB  VCL
```

## 3、Redis的应用场景到底有哪些？？

> 1、最常用的就是会话缓存 
> 2、消息队列，比如支付 
> 3、活动排行榜或计数 
> 4、发布、订阅消息(消息通知) 
> 5、商品列表、评论列表等

## 4、Redis安装

关于redis安装与相关的知识点介绍请参考 Nosql数据库服务之redis

**安装的大概步骤如下：**

Redis是c语言开发的，安装redis需要c语言的编译环境
如果没有gcc需要在线安装：yum install gcc-c++

> 第一步：获取源码包：wget http://download.redis.io/releases/redis-3.0.0.tar.gz
> 第二步：解压缩redis：tar zxvf redis-3.0.0.tar.gz
> 第三步：编译。进入redis源码目录(cd redis-3.0.0)。执行 make
> 第四步：安装。make install PREFIX=/usr/local/redis
> #PREFIX参数指定redis的安装目录

## 5、Redis数据类型
Redis一共支持五种数据类型

> 1、string(字符串)
> 2、hash(哈希)
> 3、list(列表)
> 4、set(集合)
> 5、zset（sorted set 有序集合）

**string(字符串)**
它是redis最基本的数据类型，一个**key对应一个value**，需要注意是一个键值最大存储512MB。
```bash
127.0.0.1:6379> auth myredis
OK
127.0.0.1:6379> set key "helloworld"
OK
127.0.0.1:6379> get key
"helloworld"
127.0.0.1:6379> GETSET key "nihao"
"helloworld"
127.0.0.1:6379> MSET key1 "hi" key2 "nihao" key3 "
Invalid argument(s)
127.0.0.1:6379> MSET key1 "hi" key2 "nihao" key "hello"
OK
127.0.0.1:6379> GET key1
"hi"
127.0.0.1:6379> get key2
"nihao"
127.0.0.1:6379> get key3
"hello"
127.0.0.1:6379>
```
相关命令介绍

> set 为一个Key设置value（值）
> get 获得某个key对应的value（值）
> getset 为一个Key设置value（值）并返回对应的值
> mset 为多个key设置value（值）

**hash(哈希)**
redis hash是一个**键值对的集合**， 是一个string类型的field和value的映射表，适合用于存储对象
```bash
127.0.0.1:6379> HSET redishash 1 "001"
(integer) 1
127.0.0.1:6379> HGET redishash 1
"001"
127.0.0.1:6379> HMSET redishash 1 "001" 2 "002"
OK
127.0.0.1:6379> HGET redishash 1
"001"
127.0.0.1:6379> HGET redishash 2
"002"
127.0.0.1:6379> HMGET redishash 1 2
1) "001"
2) "002"
127.0.0.1:6379>
```
相关命令介绍
hset 将Key对应的hash中的field配置为value，如果hash不存则自动创建， 
hget 获得某个hash中的field配置的值
hmset 批量配置同一个hash中的多个field值
hmget 批量获得同一个hash中的多个field值

**list(列表)**
是redis简单的字符串列表，它按插入顺序排序
```bash
127.0.0.1:6379> LPUSH word hi
(integer) 1
127.0.0.1:6379> LPUSH word hello
(integer) 2
127.0.0.1:6379> rpush word  world
(integer) 3
127.0.0.1:6379> LRANGE word 0 2
1) "hello"
2) "hi"
3) "world"
127.0.0.1:6379> LLEN word
(integer) 3
```
相关命令介绍
> lpush 向指定的列表左侧插入元素，返回插入后列表的长度
> rpush 向指定的列表右侧插入元素，返回插入后列表的长度
> llen  返回指定列表的长度
> lrange 返回指定列表中指定范围的元素值

**set(集合)**
是string类型的无序集合，也不可重复
```bash
127.0.0.1:6379> SADD redis redisset
(integer) 1
127.0.0.1:6379> SADD redis redisset1
(integer) 1
127.0.0.1:6379> SADD redis redisset2
(integer) 1
127.0.0.1:6379> SMEMBERS redis
1) "redisset"
2) "redisset1"
3) "redisset2"
127.0.0.1:6379> SADD redis redisset2
(integer) 0
127.0.0.1:6379> SMEMBERS redis
1) "redisset"
2) "redisset1"
3) "redisset2"
127.0.0.1:6379> SADD redis redisset3
(integer) 1
127.0.0.1:6379> SMEMBERS redis
1) "redisset"
2) "redisset1"
3) "redisset3"
4) "redisset2"
127.0.0.1:6379> srem redis redisset
(integer) 1
127.0.0.1:6379> SMEMBERS redis
1) "redisset1"
2) "redisset3"
3) "redisset2"
127.0.0.1:6379>
```
相关命令介绍
> sadd 添加一个string元素到key对应的set集合中，成功返回1，如果元素存在返回0
> smembers 返回指定的集合中所有的元素
> srem 删除指定集合的某个元素

**zset（sorted set 有序集合）**
是string类型的有序集合，也不可重复
sorted set中的每个元素都需要指定一个分数，根据分数对元素进行升序排序，如果多个元素有相同的分数，则以字典序进行升序排序，sorted set 因此非常适合实现排名
```bash
127.0.0.1:6379> zaddnosql 0 001
(integer) 1
127.0.0.1:6379> zaddnosql 0 002
(integer) 1
127.0.0.1:6379> zaddnosql 0 003
(integer) 1
127.0.0.1:6379> zcountnosql 0 0 
(integer) 3
127.0.0.1:6379> zcountnosql 0 3
(integer) 3
127.0.0.1:6379> zremnosql 002
(integer) 1
127.0.0.1:6379> zcountnosql 0 3
(integer) 2
127.0.0.1:6379> zscorenosql 003
"0"
127.0.0.1:6379> zrangebyscorenosql 0 10
1) "001"
2) "003"
127.0.0.1:6379> zaddnosql 1 003
(integer) 0
127.0.0.1:6379> zaddnosql 1 004
(integer) 1
127.0.0.1:6379> zrangebyscorenosql 0 10
1) "001"
2) "003"
3) "004"
127.0.0.1:6379> zaddnosql 3 005
(integer) 1
127.0.0.1:6379> zaddnosql 2 006
(integer) 1
127.0.0.1:6379> zrangebyscorenosql 0 10
1) "001"
2) "003"
3) "004"
4) "006"
5) "005"
```
相关命令介绍
> zadd  向指定的sorteset中添加1个或多个元素
> zrem  从指定的sorteset中删除1个或多个元素
> zcount 查看指定的sorteset中指定分数范围内的元素数量
> zscore 查看指定的sorteset中指定分数的元素
> zrangebyscore 查看指定的sorteset中指定分数范围内的所有元素

## 6、键值相关的命令
```bash
127.0.0.1:6379> existskey
(integer) 1
127.0.0.1:6379> existskey1
(integer) 1
127.0.0.1:6379> existskey100
(integer) 0
127.0.0.1:6379> getkey
"nihao,hello"
127.0.0.1:6379> getkey1
"hi"
127.0.0.1:6379> delkey1
(integer) 1
127.0.0.1:6379> getkey1
(nil)
127.0.0.1:6379> renamekeykey0
OK
127.0.0.1:6379> getkey
(nil)
127.0.0.1:6379> getkey0
"nihao,hello"
127.0.0.1:6379> typekey0
string
```
> exists       #确认key是否存在
> del            #删除key
> expire       #设置Key过期时间(单位秒)
> persist      #移除Key过期时间的配置
> rename     #重命名key
> type          #返回值的类型

## 7、Redis服务相关的命令
```bash
127.0.0.1:6379> select 0
OK
127.0.0.1:6379> info
# Server
redis_version:3.0.6
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:347e3eeef5029f3
redis_mode:standalone
os:Linux 3.10.0-693.el7.x86_64 x86_64
arch_bits:64
multiplexing_api:epoll
gcc_version:4.8.5
process_id:31197
run_id:8b6ec6ad5035f5df0b94454e199511084ac6fb12
tcp_port:6379
uptime_in_seconds:8514
uptime_in_days:0
hz:10
lru_clock:14015928
config_file:/usr/local/redis/redis.conf
-------------------省略N行
127.0.0.1:6379> CONFIG GET 0
(empty list or set)
127.0.0.1:6379> CONFIG GET 15
(empty list or set)
```
slect           #选择数据库(数据库编号0-15)
quit             #退出连接
info             #获得服务的信息与统计
monitor       #实时监控
config get   #获得服务配置
flushdb       #删除当前选择的数据库中的key
flushall       #删除所有数据库中的key

## 8、Redis的发布与订阅

Redis发布与订阅(pub/sub)是它的一种消息通信模式，一方发送信息，一方接收信息。
下图是三个客户端同时订阅同一个频道

下图是有新信息发送给频道1时，就会将消息发送给订阅它的三个客户端

## 9、Redis事务

Redis事务可以一次执行多条命令

> 1、发送exec命令前放入队列缓存，结束事务
> 2、收到exec命令后执行事务操作，如果某一命令执行失败，其它命令仍可继续执行
> 3、一个事务执行的过程中，其它客户端提交的请求不会被插入到事务执行的命令列表中

一个事务经历三个阶段
> 开始事务(命令:multi)
> 命令执行
> 结束事务(命令:exec)

```bash
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> setkeykey1
QUEUED
127.0.0.1:6379> getkey
QUEUED
127.0.0.1:6379> renamekeykey001
QUEUED
127.0.0.1:6379> exec
1) OK
2) "key1"
3) OK
```

## 10、Redis安全配置

可以通过修改配置文件设备密码参数来提高安全性
```bash
#requirepass foobared
```
去掉注释#号就可以配置密码
**没有配置密码的情况下查询如下**
```bash
127.0.0.1:6379> CONFIGGETrequirepass
1) "requirepass"
2) ""
```
**配置密码之后，就需要进行认证**
```bash
127.0.0.1:6379> CONFIGGETrequirepass
(error) NOAUTHAuthenticationrequired.
127.0.0.1:6379> AUTHfoobared #认证
OK
127.0.0.1:6379> CONFIGGETrequirepass
1) "requirepass"
2) "foobared"
```
## 11、Redis持久化

Redis持久有两种方式:Snapshotting(快照),Append-only file(AOF)
**Snapshotting(快照)**
```bash
> 1、将存储在内存的数据以快照的方式写入二进制文件中，如默认dump.rdb中
> 2、save 900 1 
> #900秒内如果超过1个Key被修改，则启动快照保存
> 3、save 300 10 
> #300秒内如果超过10个Key被修改，则启动快照保存
> 4、save 60 10000 
> #60秒内如果超过10000个Key被修改，则启动快照保存
**Append-only file(AOF)**

> 1、使用AOF持久时，服务会将每个收到的写命令通过write函数追加到文件中（appendonly.aof）
> 2、AOF持久化存储方式参数说明
> appendonly yes  
> #开启AOF持久化存储方式 
> appendfsync always  
> #收到写命令后就立即写入磁盘，效率最差，效果最好
> appendfsync everysec
> #每秒写入磁盘一次，效率与效果居中 
> appendfsync no 
> #完全依赖OS，效率最佳，效果没法保证
```
## 12、Redis 性能测试

自带相关测试工具
```bash
[root@test ~]# redis-benchmark --help
Usage: redis-benchmark [-h <host>] [-p <port>] [-c <clients>] [-n <requests]> [-k <boolean>]

 -h <hostname>      Server hostname (default 127.0.0.1)
 -p <port>          Server port (default 6379)
 -s <socket>        Server socket (overrides host and port)
 -a <password>      Password for Redis Auth
 -c <clients>       Number of parallel connections (default 50)
 -n <requests>      Total number of requests (default 100000)
 -d <size>          Data size of SET/GET value in bytes (default 2)
 -dbnum <db>        SELECT the specified db number (default 0)
 -k <boolean>       1=keep alive 0=reconnect (default 1)
 -r <keyspacelen>   Use random keys for SET/GET/INCR, random values for SADD
  Using this option the benchmark will expand the string __rand_int__
  inside an argument with a 12 digits number in the specified range
  from 0 to keyspacelen-1. The substitution changes every time a command
  is executed. Default tests use this to hit random keys in the
  specified range.
 -P <numreq>        Pipeline <numreq> requests. Default 1 (no pipeline).
 -q                 Quiet. Just show query/sec values
 --csv              Output in CSV format
 -l                 Loop. Run the tests forever
 -t <tests>         Only run the comma separated list of tests. The test
                    names are the same as the ones produced as output.
 -I                 Idle mode. Just open N idle connections and wait.

Examples:

 Run the benchmark with the default configuration against 127.0.0.1:6379:
   $ redis-benchmark

 Use 20 parallel clients, for a total of 100k requests, against 192.168.1.1:
   $ redis-benchmark -h 192.168.1.1 -p 6379 -n 100000 -c 20

 Fill 127.0.0.1:6379 with about 1 million keys only using the SET test:
   $ redis-benchmark -t set -n 1000000 -r 100000000

 Benchmark 127.0.0.1:6379 for a few commands producing CSV output:
   $ redis-benchmark -t ping,set,get -n 100000 --csv

 Benchmark a specific command line:
   $ redis-benchmark -r 10000 -n 10000 eval'return redis.call("ping")' 0

 Fill a list with 10000 random elements:
   $ redis-benchmark -r 10000 -n 10000 lpush mylist __rand_int__

 On user specified command lines __rand_int__ is replaced with a random integer
 with a range of values selected by the -r option.
```
**实际测试同时执行100万的请求**
```bash
[root@test ~]# redis-benchmark -n 1000000 -q
PING_INLINE: 152578.58 requests per second
PING_BULK: 150308.14 requests per second
SET: 143266.47 requests per second
GET: 148632.58 requests per second
INCR: 145857.64 requests per second
LPUSH: 143781.45 requests per second
LPOP: 147819.66 requests per second
SADD: 138350.86 requests per second
SPOP: 134282.27 requests per second
LPUSH (needed tobenchmark LRANGE): 141302.81 requests per second
LRANGE_100 (first100 elements): 146756.67 requests per second
LRANGE_300 (first300 elements): 148104.27 requests per second
LRANGE_500 (first450 elements): 152671.75 requests per second
LRANGE_600 (first600 elements): 148104.27 requests per second
MSET (10keys): 132731.62 requests per second
```
## 13、Redis的备份与恢复

**Redis自动备份有两种方式**
第一种是通过dump.rdb文件实现备份
第二种使用aof文件实现自动备份

**dump.rdb备份**
Redis服务默认的自动文件备份方式(AOF没有开启的情况下)，在服务启动时，就会自动从dump.rdb文件中去加载数据。
具体配置在redis.conf
```
save 900 1 
save 300 10 
save 60 10000
```
也可以手工执行save命令实现手动备份
```bash
127.0.0.1:6379> setnamekey
OK
127.0.0.1:6379> SAVE
OK
127.0.0.1:6379> setnamekey1
OK
127.0.0.1:6379> BGSAVE
Backgroundsavingstarted
```
redis快照到dump文件时，会自动生成dump.rdb的文件
```bash
# The filename where to dump the DB
dbfilename dump.rdb
-rw-r--r-- 1 root root   253 Apr 17 20:17 dump.rdb
```
**SAVE命令**表示使用主进程将当前数据库快照到dump文件
**BGSAVE命令**表示，主进程会fork一个子进程来进行快照备份
两种备份不同之处，前者会阻塞主进程，后者不会。

**恢复举例**
```bash
# Note that you must specify a directory here, not a file name.
dir /usr/local/redisdata/
备份文件存储路径
127.0.0.1:6379> CONFIG GET dir
1) "dir"
2) "/usr/local/redisdata"
127.0.0.1:6379> set key 001
OK
127.0.0.1:6379> set key1 002
OK
127.0.0.1:6379> set key2 003
OK
127.0.0.1:6379> save
OK
```
**将备份文件备份到其它目录**
```bash
[root@test ~]# ll /usr/local/redisdata/
total 4
-rw-r--r-- 1 root root 49 Apr 17 21:24 dump.rdb
[root@test ~]# date
Tue Apr 17 21:25:38 CST 2018
[root@test ~]# cp ./dump.rdb /tmp/
```
**删除数据**
```bash
127.0.0.1:6379> delkey1
(integer) 1
127.0.0.1:6379> getkey1
(nil)
```
**关闭服务，将原备份文件拷贝回save备份目录**
```bash
[root@test ~]# redis-cli -a foobared shutdown
[root@test ~]# lsof -i :6379
[root@test ~]# cp /tmp/dump.rdb /usr/local/redisdata/
cp: overwrite ‘/usr/local/redisdata/dump.rdb’? y
[root@test ~]# redis-server /usr/local/redis/redis.conf &
[1] 31487
```
**登录查看数据是否恢复**
```bash
[root@test ~]# redis-cli -a foobared
127.0.0.1:6379> mget key key1 key2
1) "001"
2) "002"
3) "003"
```
**AOF自动备份**
redis服务默认是关闭此项配置
```bash
###### APPEND ONLY MODE ##########
appendonly no

# The name of the append only file (default: "appendonly.aof")
appendfilename "appendonly.aof"

# appendfsync always
appendfsync everysec
# appendfsync no
```
配置文件的相关参数，前面已经详细介绍过。
AOF文件备份，是备份所有的历史记录以及执行过的命令，和mysql binlog很相似，在恢复时就是重新执次一次之前执行的命令，需要注意的就是在恢复之前，和数据库恢复一样需要手工删除执行过的del或误操作的命令。

> AOF与dump备份不同
> 1、aof文件备份与dump文件备份不同
> 2、服务读取文件的优先顺序不同，会按照以下优先级进行启动
>     如果只配置AOF,重启时加载AOF文件恢复数据
>     如果同时 配置了RBD和AOF,启动是只加载AOF文件恢复数据
>     如果只配置RBD,启动时将加载dump文件恢复数据

注意：只要配置了aof，但是没有aof文件，这个时候启动的数据库会是空的

14、Redis 生产优化介绍

1、内存管理优化 
```bash
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
list-max-ziplist-entries 512
list-max-ziplist-value 64
#list的成员与值都不太大的时候会采用紧凑格式来存储，相对内存开销也较小
```
> 在linux环境运行Redis时，如果系统的内存比较小，这个时候自动备份会有可能失败，需要修改系统的vm.overcommit_memory 参数，这个参数是linux系统的内存分配策略
>     0 表示内核将检查是否有足够的可用内存供应用进程使用；如果有足够的可用内存，内存申请允许；否则，内存申请失败，并把错误返回给应用进程。
>     1 表示内核允许分配所有的物理内存，而不管当前的内存状态如何。
>     2 表示内核允许分配超过所有物理内存和交换空间总和的内存
>  Redis官方的说明是，建议将vm.overcommit_memory的值修改为1，可以用下面几种方式进行修改：
> （1）编辑/etc/sysctl.conf 改vm.overcommit_memory=1，然后sysctl -p 使配置文件生效
> （2）sysctl vm.overcommit_memory=1
>  （3）echo 1 > /proc/sys/vm/overcommit_memory

**2、内存预分配**
**3、持久化机制**
>    定时快照：效率不高，会丢失数据
>    AOF：保持数据完整性（一个实例的数量不要太大2G最大）

优化总结
> 1）根据业务需要选择合适的数据类型
> 2）当业务场景不需持久化时就关闭所有持久化方式（采用ssd磁盘来提升效率）
> 3）不要使用虚拟内存的方式，每秒实时写入AOF
> 4）不要让REDIS所在的服务器物理内存使用超过内存总量的3/5
> 5）要使用maxmemory
> 6）大数据量按业务分开使用多个redis实例

## 15、Redis集群应用

集群是将多个redis实例集中在一起，实现同一业务需求，或者实现高可用与负载均衡

到底有哪些集群方案呢？？
1、haproxy+keepalived+redis集群
1）通过redis的配置文件，实现主从复制、读写分离
2）通过haproxy的配置，实现负载均衡，当从故障时也会及时从集群中T除
3）利用keepalived来实现负载的高可用

2、redis官方Sentinel集群管理工具

Redis集群生产环境高可用方案实战过程
1）sentinel负责对集群中的主从服务监控、提醒和自动故障转移
2）redis集群负责对外提供服务
关于redis sentinel cluster集群配置可参考

3、Redis Cluster 
Redis Cluster是Redis的分布式解决方案，在Redis 3.0版本正式推出的，有效解决了Redis分布式方面的需求。当遇到单机内存、并发、流量等瓶颈时，可以采用Cluster架构达到负载均衡的目的。

1）官方推荐，毋庸置疑。
2）去中心化，集群最大可增加1000个节点，性能随节点增加而线性扩展。
3）管理方便，后续可自行增加或摘除节点，移动分槽等等。
4）简单，易上手。


来源：来自：民工哥Linux运维（微信号：mingongge-linux）