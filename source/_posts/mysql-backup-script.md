---
title: mysql 备份脚本
date: 2013-11-04 08:43:00
updated: 2015-11-07 08:45:09
tags: 
- nginx
- ddos
categories: 
- linux

---
在服务器上找到的一个很久前写的mysql备份脚本，这台服务器有一块挂载到/data的单独硬盘，专门储存mysql的备份文件，并自动检测磁盘容量，磁盘容量超过95％时，自动删除最老的10个备份.


<!--more-->


    #!/bin/bash
    time=`date +%Y-%m-%d_%H-%M-%S`
    db_host=database.com
    db_name=db_user
    db_user=db_pass
    db_passwd=database
    /usr/bin/mysqldump -R -h$db_host -u$db_user -p$db_passwd $db_name >/tmp/db_"$time".sql
    if [  -s "/tmp/db_"$time".sql" ];then
        cd /tmp/
        tar -zcvf /data/backup/db_"$time".tar.gz db_"$time".sql
    fi
    rm -rf /tmp/db_"$time".sql
    df -k /data | grep 'dev' > disk
    while read LINE
            do
            dev=`echo $LINE | awk '{print $1}'`
            use=`echo $LINE | awk '{print $5+0}'`
            mnt=`echo $LINE | awk '{print $6}'`
        echo $dev + $use + $mnt
            if [ $use -ge 95 ];then
                    ls -t /data/backup/ |tail  -10 | xargs rm -rf
            fi
    done < disk
    rm -rf disk

