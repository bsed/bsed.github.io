---
title: "MySQL实例的优化配置"
categories: [ "Linux" ]
tags: [ "redis" ]
draft: false
slug: "optimal-allocation-of-mysql-instances"
date: "2015-11-28 23:15:00"
---

## 1、系统环境

1、操作系统：  CentOS release 7 (Final)
2、Redis版本： Redis server v=2.6.14 sha=00000000:0 malloc=jemalloc-3.2.0 bits=64
3、Redis启动脚本: /etc/init.d/redis


<!--more-->


```bash
#!/bin/sh
#
# redis        init file for starting up the redis daemon
#
# chkconfig:   - 20 80
# description: Starts and stops the redis daemon.

# Source function library.
. /etc/rc.d/init.d/functions

name="redis-server"
exec="/usr/sbin/$name"
pidfile="/var/run/redis/redis.pid"
REDIS_CONFIG="/etc/redis.conf"

[ -e /etc/sysconfig/redis ] && . /etc/sysconfig/redis

lockfile=/var/lock/subsys/redis

start() {
    [ -f $REDIS_CONFIG ] || exit 6
    [ -x $exec ] || exit 5
    echo -n $"Starting $name: "
    daemon "$exec $REDIS_CONFIG"
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile && ps -ef | grep '/usr/sbin/redis-server /etc/redis.conf' | grep -v grep  | awk '{print $2}' >> $pidfile
    return $retval
}

stop() {
    echo -n $"Stopping $name: "
    killproc -p $pidfile $name
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}

restart() {
    stop
    start
}

reload() {
    false
}

rh_status() {
    status -p $pidfile $name
}

rh_status_q() {
    #rh_status >/dev/null 2>&1
    rh_status
}


case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart)
        $1
        ;;
    reload)
        rh_status_q || exit 7
        $1
        ;;
    force-reload)
        force_reload
        ;;
    status)
        rh_status
        ;;
    condrestart|try-restart)
        rh_status_q || exit 0
        restart
        ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart}"
        exit 2
esac
exit $?
```
## 3、主Redis配置文件: `/etc/redis/redis_27001.conf`
```bash
daemonize yes
pidfile /home/jm/data/log/redis_27001.pid
port 27001
bind 192.168.40.105
timeout 0
maxclients 5000000
maxmemory 5368709120
loglevel notice
logfile /home/jm/data/log/redis_27001.log
databases 16
save 43200 10000
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump.rdb
dir /home/jm/data/redis/27001
slave-serve-stale-data yes
#slaveof no one
slave-read-only yes
repl-ping-slave-period 1
slave-priority 100
appendonly no
#appendfilename appendonly.aof
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 50
auto-aof-rewrite-min-size 128mb
lua-time-limit 5000
slowlog-log-slower-than 10000
slowlog-max-len 128
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
list-max-ziplist-entries 512
list-max-ziplist-value 64
set-max-intset-entries 512
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
activerehashing yes
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
```
## 4、从Redis配置文件: `/etc/redis/redis_27001.conf`
```bash
daemonize yes
pidfile /var/run/redis/redis_27001.pid
port 27001
bind 192.168.40.127
tcp-backlog 2048
timeout 0
tcp-keepalive 0
maxclients 5000000
maxmemory 5368709120
loglevel notice
logfile /var/log/redis/redis_27001.log
databases 16
save 43200 10000
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump.rdb
dir /home/jm/redis/27001
slave-serve-stale-data yes
slaveof 192.168.40.126 27001
slave-read-only yes
repl-ping-slave-period 1
repl-disable-tcp-nodelay no
slave-priority 100
appendonly yes
appendfilename appendonly.aof
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 50
auto-aof-rewrite-min-size 128mb
lua-time-limit 5000
slowlog-log-slower-than 10000
slowlog-max-len 128
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
list-max-ziplist-entries 512
list-max-ziplist-value 64
set-max-intset-entries 512
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
activerehashing yes
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
hz 10
aof-rewrite-incremental-fsync yes
```