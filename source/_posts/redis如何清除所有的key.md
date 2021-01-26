---
title: "redis如何清除所有的key"
categories: [ "Linux" ]
tags: [ "redis" ]
draft: false
slug: "redis-how-to-clear-all-key"
date: "2015-08-20 23:10:00"
---

## 1、使用cli
```bash
redis-cli -h 192.168.0.100 -p 27001 flushdb   #清除一个数据库
redis-cli -h 192.168.0.100 -p 27001 flushall  #清除整个redis数据
```


<!--more-->


##2、 使用shell
```bash
#删除过期的
redis-cli -h 192.168.0.100 -p 27001 keys "*" | while read LINE ; do TTL=`redis-cli ttl $LINE`; if [ $TTL -eq -1 ]; then echo "Del $LINE"; RES=`redis-cli -h 192.168.0.100 -p 27001 del $LINE`; fi; done;
#删除 3600秒之后过期的
redis-cli -h 192.168.0.100 -p 27001 keys  "*" | while read LINE ; do TTL=`redis-cli ttl $LINE`; if [ $TTL -ge  3600 ]; then echo "Del $LINE"; RES=`redis-cli -h 192.168.0.100 -p 27001 del $LINE`; fi; done;
#删除某些前缀的
redis-cli -h 192.168.0.100 -p 27001  "126.com*" | xargs redis-cli -h 192.168.0.100 -p 27001 del
```