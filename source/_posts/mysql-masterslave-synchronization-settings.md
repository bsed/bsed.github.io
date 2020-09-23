---
title:  mysql主从同步设置
date: 2012-06-18 08:19:00
updated: 2015-11-07 08:23:15
tags: 
- amowa
categories: 
- java

---
> 主从同步配置步骤如下：
> 
> 环境：MySQL server 5.1.60 64位   CentOS 5.3 64 bit位


## 一、主服务器配置

编辑主服务器配置文件/etc/my.cnf，如果该文件不存在请执行

     cp /usr/share/mysql/my-medium.cnf /etc/my.cnf

【有关MySQL参数调优不在本文描述】


<!--more-->


[mysqld] 加入:

    max_connect_errors = 720000
    binlog-do-db=DATABASE

检查以下选项：

    log-bin=mysql-bin
    server-id       = 1
    binlog_format=mixed

开启innoDB 配置：

    # Uncomment the following if you are using InnoDB tables
    innodb_data_home_dir = /var/lib/mysql
    innodb_data_file_path = ibdata1:10M:autoextend
    innodb_log_group_home_dir = /var/lib/mysql
    # You can set .._buffer_pool_size up to 50 - 80 %
    # of RAM but beware of setting memory usage too high
    innodb_buffer_pool_size = 16M
    innodb_additional_mem_pool_size = 2M
    # Set .._log_file_size to 25 % of buffer pool size
    innodb_log_file_size = 5M
    innodb_log_buffer_size = 8M
    innodb_flush_log_at_trx_commit = 1
    innodb_lock_wait_timeout = 50

重新启动： mysql

    service mysql start

在主服务器上创建主从同步用户帐号：

    Create User rep_slave IDENTIFIED BY 'rep_slave';
    GRANT REPLICATION SLAVE ON *.* TO 'DATABASE_slave'@'%';
    FLUSH PRIVILEGES;

二、从服务器配置

停止服务器

    service mysql stop

    vi /etc/my.cnf

[mysqld] 加入：

    max_connect_errors = 720000
    server-id       = 2
    master-host     =   192.168.1.244
    master-user     =   micrblog_slave
    master-password =   DATABASE
    replicate-do-db=DATABASE
    relay-log = /var/lib/mysql/slave-relay.log
    relay-log-index = /var/lib/mysql/slave-relay-log.index
    master-connect-retry=60
    master-port=3306

开启innoDB 配置：

    # Uncomment the following if you are using InnoDB tables
    innodb_data_home_dir = /var/lib/mysql
    innodb_data_file_path = ibdata1:10M:autoextend
    innodb_log_group_home_dir = /var/lib/mysql
    # You can set .._buffer_pool_size up to 50 - 80 %
    # of RAM but beware of setting memory usage too high
    innodb_buffer_pool_size = 16M
    innodb_additional_mem_pool_size = 2M
    # Set .._log_file_size to 25 % of buffer pool size
    innodb_log_file_size = 5M
    innodb_log_buffer_size = 8M
    innodb_flush_log_at_trx_commit = 1
    innodb_lock_wait_timeout = 50


重新启动： mysql

    service mysql start

## 三、数据导入到从服务器

    mysql -u root -p (log into MySQL)
    FLUSH TABLES WITH READ LOCK;


从主服务器导出数据

    mysqldump --default-character-set=utf8 -h192.168.1.244 -uroot -p　DATABASE >DATABASE-exp1.sql


将文件传到从服务器上并执行导入：

    mysql --default-character-set=utf8 -h192.168.1.129 -uroot -p -D  DATABASE < DATABASE-exp1.sql


## 四、启动主从同步

### 4.1查看主服务器bin-log状态

SHOW MASTER STATUS;

    mysql> show master status;
    
    +------------------+----------+--------------+------------------+
    
    | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
    
    +------------------+----------+--------------+------------------+
    
    | mysql-bin.000002 |      106 | DATABASE    |                  |
    
    +------------------+----------+--------------+------------------+

### 4.2 登录到从服务器执行

      slave stop;

CHANGE MASTER TO MASTER_HOST='192.168.1.244', MASTER_USER='micrblog_slave', MASTER_PASSWORD='DATABASE', MASTER_LOG_FILE='mysql-bin.000002', MASTER_LOG_POS=106;

 slave start;

### 4.3 检查从服务器状态

show slave status \G

    mysql> show slave status \G;
    
    
    *************************** 1. row ***************************
    
                 Slave_IO_State: Waiting for master to send event
    
                 Master_Host: 192.168.1.244
    
                 Master_User: DATABASE_slave
    
                 Master_Port: 3306
    
                 Connect_Retry: 60
    
                 Master_Log_File: mysql-bin.000004
    
                 Read_Master_Log_Pos: 896
    
                 Relay_Log_File: slave-relay.000002
    
                 Relay_Log_Pos: 1041
    
                  Relay_Master_Log_File: mysql-bin.000004
    
                 Slave_IO_Running: Yes
    
                 Slave_SQL_Running: Yes
    
                 Replicate_Do_DB: DATABASE
    
                  Replicate_Ignore_DB:
    
                  Replicate_Do_Table:
    
                   Replicate_Ignore_Table:
    
                    Replicate_Wild_Do_Table:
    
                     Replicate_Wild_Ignore_Table:
    
                  Last_Errno: 0
    
                  Last_Error:
    
                  Skip_Counter: 0
    
                    Exec_Master_Log_Pos: 896
    
                  Relay_Log_Space: 1192
    
                  Until_Condition: None
    
                  Until_Log_File:
    
                  Until_Log_Pos: 0
    
             Master_SSL_Allowed: No
    
             Master_SSL_CA_File:
    
             Master_SSL_CA_Path:
    
             Master_SSL_Cert:
    
             Master_SSL_Cipher:
    
             Master_SSL_Key:
    
             Seconds_Behind_Master: 0
    
                Master_SSL_Verify_Server_Cert: No
    
              Last_IO_Errno: 0
    
              Last_IO_Error:
    
              Last_SQL_Errno: 0
    
              Last_SQL_Error:
    
     1 row in set (0.00 sec)

### 4.4  释放Read Lock

  回到主服务器 释放Read Lock 【之前执行的 FLUSH TABLES WITH READ LOCK;】

    unlock tables;

## 五、测试

 等待5分钟--10分钟

 连上两个数据库，查看最新的消息是否同步:


     set names utf8;
    
     select msg_id, msg_text from star_message order by msg_id desc limit 3;

参考文章：

[http://aciddrop.com/2008/01/10/step-by-step-how-to-setup-mysql-database-replication/](http://aciddrop.com/2008/01/10/step-by-step-how-to-setup-mysql-database-replication/)

[http://my.oschina.net/captaintheron/blog/4235](http://my.oschina.net/captaintheron/blog/4235)








