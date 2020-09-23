---
title: Docker 搭建redis 集群
date: 2016-01-05 15:45:00
updated: 2017-02-12 16:02:05
tags: 
- linux
- wifi
categories: 
- linux

---
## 准备工作

创建一个目录用保存redis-slave的配置文件
```bash
mkdir/data/docker/redis/  
wget https://raw.githubusercontent.com/antirez/redis/3.0/redis.conf \  
-O /data/docker/redis/redis.conf
```
## 安装redis


<!--more-->


```bash
docker pull redis
```
## 修改redis-slave配置文件
```bash
# 修改 /data/docker/redis/redis.conf 的slaveof属性为redis-master 6379
cd /data/docker/redis/  
sed -i 's/# slaveof <masterip> <masterport>/slaveof redis-master 6379/g' redis.conf
```
## 启动服务

启动`redis-master`、 `redis-slave1`、 `redis-slave2`、 `redis-slave3`
```bash
docker run --name redis-master -p 6379:6379 -d redis

docker run --link redis-master:redis-master -v /data/docker/redis/redis.conf:/usr/local/etc/redis/redis.conf --name redis-slave1 -d redis redis-server /usr/local/etc/redis/redis.conf

docker run --link redis-master:redis-master -v /data/docker/redis/redis.conf:/usr/local/etc/redis/redis.conf --name redis-slave2 -d redis redis-server /usr/local/etc/redis/redis.conf

docker run --link redis-master:redis-master -v /data/docker/redis/redis.conf:/usr/local/etc/redis/redis.conf --name redis-slave3 -d redis redis-server /usr/local/etc/redis/redis.conf
```
## 测试

用本地的 `redis-cli` 连接到 `master`
```bash
redis-cli  
127.0.0.1:6379> info # 查看详细信息  
```