---
title: Redis rdb dump的问题
date: 2012-08-28 18:56:00
updated: 2014-11-11 19:00:42
tags: 
- golang
- freetype
categories: 
- go

---
rdb是redis支持的一种持久化方式，在进行rdb dump时，redis直接将内存中的所有数据写到磁盘的文件上，以实现持久化。以下三种情况会使用rdb dump：


<!--more-->


通过`save/bgsave`命令保存数据
满足配置文件中的save指令条件触发持久化操作
Slave初始连接上Master时，Master会dump一份数据，传输给从库。
rdb有个隐蔽的问题需要注意，就是Redis的工作路径和配置的rdb dump文件的路径必须在同一个硬盘分区上。如果处在不同的分区，所有这些需要使用rdb dump的操作都会失败。

这个问题的产生是由于redis处理dump文件的方式。redis会先把内存中的数据dump到临时文件，在dump完成之后，再从临时文件建立一个硬链接到指定的rdb文件保存位置。redis使用这样的操作以避免出现未完成的dump文件的情况，此部分代码如下：

    if (rename(tmpfile,filename) == -1) { 
        redisLog(REDIS_WARNING,"Error moving temp DB file on the final destination: %s", strerror(errno)); 
        unlink(tmpfile); 
        return REDIS_ERR;
    }

临时文件使用的是redis的工作路径，如果没有修改redis配置文件中的dir配置项，这个工作路径也就是启动redis所在的路径。如果工作路径和rdb文件保存路径不在同一个分区上，那么rdb dump操作就会在建立硬链接的时候失败。
