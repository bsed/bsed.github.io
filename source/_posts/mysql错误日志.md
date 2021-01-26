---
title: "mysql错误日志"
categories: [ "SQL" ]
tags: [ "mysql","error" ]
draft: false
slug: "mysql"
date: "2012-02-20 20:18:00"
---

> mysqld启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息

## I. 开启、设置错误日志

#### 在my.ini中设置

```
[mysqld]
log-error=[path/[file_name]]
```


<!--more-->


`path为日志文件所在的目录路径,filename为日志文件名。`

修改配置项后，需要重启mysql服务才生效.

如：

`log-error = "D:/phpStudy/MySQL/MySQL_Error_Log/error-log.err"`

## II. 查看错误日志

可直接用文本查看器查看最后一行有一个 (Errcode: 20)

MySQL 附带了工具perror，将错误码转成适合阅读的内容：

`> bin perror 28`


`> show variables; 查看mysql变量 show variables like 'log_error'`


## III. 分析错误日志


- 最后一行有一个 (Errcode: 20），用perror查看对于的错误解释
- 时间[错误级别]错误信息
- 管理（删除）错误日志
  - 运行状态下，命令行创建
    在运行状态下删除错误日志文件后，mysql并不会自动创建日志文件。flush logs在重新加载日志的时候，
    如果文件不存在，则会自动创建。所以，在删除错误日志之后，如果需要重建日志文件需要在服务器端执行以下命令：
    `mysqladmin -u root -p flush-logs`
    或者在客户端登录mysql数据库，执行flush logs语句
    `flush logs`
  - 手动删除文件，手动执行命令创建
    A. 手动删除文件
    B. 手动执行 flush logs; err文件恢复了
    C. 打开err文件，应该是里面什么都没有