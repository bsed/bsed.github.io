---
title: "Linux下的mongodb启动脚本"
categories: [ "Linux" ]
tags: [ "mongodb","bash" ]
draft: false
slug: "mongodb-boot-script-under-linux"
date: "2018-08-24 08:18:00"
---

将代码保存到 `/etc/init.d/mongodb`，然后使用 `chmod +x /etc/init.d/mongodb` 添加执行权限。

现在，就可以使用 service 命令来控制 mongodb 了：

```bash
service mongodb start|stop|restart
#或
/etc/init.d/mongodb start|stop|restart
```
非常简单，对吧。
<!--more-->
代码如下：
```bash
#!/bin/sh
#
#mongod - Startup script for mongod
#
# chkconfig: - 85 15
# description: Mongodb database.
# processname: mongod
# Source function library

. /etc/rc.d/init.d/functions
# things from mongod.conf get there by mongod reading it
# OPTIONS
OPTIONS=" --dbpath=/home/data/mongodb/ --logpath=/home/data/mongodb/mongodb.log --logappend &"
#mongod
mongod="/usr/local/mongodb/bin/mongod"
lockfile=/var/lock/subsys/mongod
start()
{
  echo -n $"Starting mongod: "
  daemon $mongod $OPTIONS
  RETVAL=$?
  echo
  [ $RETVAL -eq 0 ] && touch $lockfile
}

stop()
{
  echo -n $"Stopping mongod: "
  killproc $mongod -QUIT
  RETVAL=$?
  echo
  [ $RETVAL -eq 0 ] && rm -f $lockfile
}

restart () {
        stop
        start
}
ulimit -n 12000
RETVAL=0

case "$1" in
  start)
    start
    ;;
  stop)
    stop
    ;;
  restart|reload|force-reload)
    restart
    ;;
  condrestart)
    [ -f $lockfile ] && restart || :
    ;;
  status)
    status $mongod
    RETVAL=$?
    ;;
  *)
    echo "Usage: $0 {start|stop|status|restart|reload|force-reload|condrestart}"
    RETVAL=1
esac
exit $RETVAL
```