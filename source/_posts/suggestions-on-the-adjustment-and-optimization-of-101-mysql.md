---
title: 101 个 MySQL 的调整和优化的建议
date: 2019-01-19 00:39:00
updated: 2019-01-22 10:54:57
tags: 
- c
categories: 
- default

---
MySQL是一个功能强大的开源数据库。随着越来越多的数据库驱动的应用程序，人们一直在推动MySQL发展到它的极限。这里是101条调节和优化MySQL安装的技巧。一些技巧是针对特定的安装环境的，但这些思路是通用的。我已经把他们分成几类，来帮助你掌握更多MySQL的调节和优化技巧。
![36101-ieiknz0ixlk.png](https://imgs.gnux.cn/usr/uploads/2019/01/99411123.png)
### MySQL 服务器硬件和操作系统调整


<!--more-->


- 拥有足够的物理内存来把整个InnoDB文件加载到内存中——在内存中访问文件时的速度要比在硬盘中访问时快的多。
- 不惜一切代价避免使用Swap交换分区 – 交换时是从硬盘读取的，它的速度很慢。
- 使用电池供电的RAM（注：RAM即物理内存）。
- 使用高级的RAID（注：Redundant Arrays of Inexpensive Disks，即磁盘阵列） – 最好是RAID10或更高。
- 避免RAID5（注：一种存储性能、数据安全和存储成本兼顾的存储解决方案） – 确保数据库完整性的校验是要付出代价的。
- 将操作系统和数据分区分开，不仅仅是逻辑上，还包括物理上 – 操作系统的读写操作会影响数据库的性能。
- 把MySQL临时空间和复制日志与数据放到不同的分区 – 当数据库后台从磁盘进行读写操作时会影响数据库的性能。
- 更多的磁盘空间等于更快的速度。
- 更好更快的磁盘。
- 使用SAS（注： Serial Attached SCSI，即串行连接SCSI）代替SATA（注：SATA，即串口硬盘）。
- 较小的硬盘 比 较大的硬盘快，尤其是在RAID配置的情况下。
- 使用电池支持的高速缓存RAID控制器。
- 避免使用软件磁盘阵列。
- 考虑为数据分区使用固态IO卡 (不是磁盘驱动器) – 这些卡能够为几乎任何数量的数据支持2GB/s的写入速度。
- 在Linux中设置**swappiness的值为0** – 在数据库服务器中没有理由缓存文件，这是一个服务器或台式机的优势。
- 如果可以的话，使用 noatime 和 nodirtime 挂载文件系统 – 没有理由更新访问数据库文件的修改时间。
- 使用 XFS 文件系统 – 一种比ext3更快、更小的文件系统，并且有许多日志选项， 而且ext3 已被证实与MySQL有双缓冲问题。
- 调整 XFS 文件系统日志和缓冲变量 – 为了最高性能标准。
- 在 Linux 系统中, 使用 NOOP 或者 DEADLINE IO 定时调度程序 – 同 NOOP 和 DEADLINE定时调度程序相比，这个 CFQ 和 ANTICIPATORY 定时调度程序 显得非常慢。
- 使用64位的操作系统 – 对于MySQL，会有更大的内存支持和使用。
- 删除服务器上未使用的安装包和守护进程 – 更少的资源占用。
- 把使用MySQL的host和你的MySQL host放到一个hosts文件中 – 没有DNS查找。
- 切勿强制杀死一个MySQL进程 – 你会损坏数据库和正在运行备份的程序。
- 把服务器贡献给MySQL – 后台进程和其他服务能够缩短数据库占用CPU的时间。

### MySQL 配置

- 当写入时，使用 innodb_flush_method=O_DIRECT 来避免双缓冲。
- 避免使用 O_DIRECT 和 EXT3 文件系统 – 你将序列化所有要写入的。
- 分配足够的 innodb_buffer_pool_size 来加载整个 InnoDB 文件到内存中– 少从磁盘中读取。
- 不要将 innodb_log_file_size 参数设置太大， 这样可以更快同时有更多的磁盘空间 – 丢掉多的日志通常是好的，在数据库崩溃后可以降低恢复数据库的时间。
- 不要混用 innodb_thread_concurrency 和 thread_concurrency 参数– 这2个值是不兼容的。
- 分配一个极小的数量给 max_connections 参数 – 太多的连接会用尽RAM并锁定MySQL服务。
- 保持 thread_cache 在一个相对较高的数字，大约 16 – 防止打开连接时缓慢。
- 使用skip-name-resolve参数 – 去掉 DNS 查找。
- 如果你的查询都是重复的，并且数据不常常发生变化，那么可以使用查询缓存。但是如果你的数据经常发生变化，那么使用查询缓存会让你感到失望。
- 增大temp_table_size值，以防止写入磁盘
- 增大max_heap_table_size值，以防止写入磁盘
- 不要把sort_buffer_size值设置的太高，否则的话你的内存将会很快耗尽
- 根据key_read_requests和key_reads值来决定key_buffer的大小，一般情况下key_read_requests应该比key_reads值高，否则你不能高效的使用key_buffer
- 将innodb_flush_log_at_trx_commit设置为0将会提高性能，但是如果你要保持默认值（1）的话，那么你就要确保数据的完整性，同时你也要确保复制不会滞后。
- 你要有一个测试环境，来测试你的配置，并且在不影响正常生产的情况下，可以常常进行重启。

### MySQL模式优化

- 保持你的数据库整理性。
- 旧数据归档 – 删除多余的行返回或搜索查询。
- 将您的数据加上索引.
- 不要过度使用索引，比较与查询.
- 压缩文字和BLOB数据类型 – 以节省空间和减少磁盘读取次数.
- UTF 8和UTF16都低于latin1执行效率.
- 有节制地使用触发器.
- 冗余数据保持到最低限度 – 不重复不必要的数据.
- 使用链接表，而不是扩展行.
- 注意数据类型，在您的真实数据中，尽可能使用最小的一个.
- 如果其他数据经常被用于查询时，而BLOB / TEXT数据不是，就把BLOB / TEXT数据从其他数据分离出来.
- 检查和经常优化表.
- 经常重写InnoDB表优化.
- 有时，当添加列时删除索引，然后在添加回来索引，这样就会更快.
- 针对不同的需求，使用不同的存储引擎.
- 使用归档存储引擎日志表或审计表-这是更有效地写道.
- 会话数据存储在缓存（memcache）的而不是MySQL中 – 缓存允许自动自动填值的，并阻止您创建难以读取和写入到MySQL的时空数据.
- 存储可变长度的字符串时使用VARCHAR而不是CHAR –节省空间，因为固定长度的CHAR，而VARCHAR长度不固定（UTF8不受此影响）.
- 逐步进行模式的变化 – 一个小的变化，可以有巨大的影响.
- 在开发环境中测试所有模式，反映生产变化.
- 不要随意更改你的配置文件中的值，它可以产生灾难性的影响.
- 有时候，在MySQL的configs少即是多.
- 有疑问时使用一个通用的MySQL配置文件.

![89792-jbpgz8lbx9e.png](https://imgs.gnux.cn/usr/uploads/2019/01/2554432040.png)


### 查询优化

- 使用慢查询日志去发现慢查询。
- 使用执行计划去判断查询是否正常运行。
- 总是去测试你的查询看看是否他们运行在最佳状态下 –久而久之性能总会变化。
- 避免在整个表上使用count(*),它可能锁住整张表。
- 使查询保持一致以便后续相似的查询可以使用查询缓存。
- 在适当的情形下使用GROUP BY而不是DISTINCT。
- 在WHERE, GROUP BY和ORDER BY子句中使用有索引的列。
- 保持索引简单,不在多个索引中包含同一个列。
- 有时候MySQL会使用错误的索引,对于这种情况使用USE INDEX。
- 检查使用SQL_MODE=STRICT的问题。
- 对于记录数小于5的索引字段，在UNION的时候使用LIMIT不是是用OR.
- 为了 避免在更新前SELECT，使用INSERT ON DUPLICATE KEY或者INSERT IGNORE ,不要用UPDATE去实现。
- 不要使用 MAX,使用索引字段和ORDER BY子句。
- 避免使用ORDER BY RAND().
- LIMIT M，N实际上可以减缓查询在某些情况下，有节制地使用。
- 在WHERE子句中使用UNION代替子查询。
- 对于UPDATES（更新），使用 SHARE MODE（共享模式），以防止独占锁。
- 在重新启动的MySQL，记得来温暖你的数据库，以确保您的数据在内存和查询速度快。
- 使用DROP TABLE，CREATE TABLE DELETE FROM从表中删除所有数据。
- 最小化的数据在查询你需要的数据，使用*消耗大量的时间。
- 考虑持久连接，而不是多个连接，以减少开销。
- 基准查询，包括使用服务器上的负载，有时一个简单的查询可以影响其他查询。
- 当负载增加您的服务器上，使用SHOW PROCESSLIST查看慢的和有问题的查询。
- 在开发环境中产生的镜像数据中 测试的所有可疑的查询。

### MySQL 备份过程

- 从二级复制服务器上进行备份。
- 在进行备份期间停止复制，以避免在数据依赖和外键约束上出现不一致。
- 彻底停止MySQL，从数据库文件进行备份。
- 如果使用 MySQL dump进行备份，请同时备份二进制日志文件 – 确保复制没有中断。
- 不要信任LVM 快照 – 这很可能产生数据不一致，将来会给你带来麻烦。
- 为了更容易进行单表恢复，以表为单位导出数据 – 如果数据是与其他表隔离的。
- 当使用mysqldump时请使用 –opt。
- 在备份之前检查和优化表。
- 为了更快的进行导入，在导入时临时禁用外键约束。
- 为了更快的进行导入，在导入时临时禁用唯一性检测。
- 在每一次备份后计算数据库，表以及索引的尺寸，以便更够监控数据尺寸的增长。
- 通过自动调度脚本监控复制实例的错误和延迟。
- 定期执行备份。
- 定期测试你的备份。

最后 : 执行MySQL 监控: **Monitis Unveils The World’s First Free On-demand MySQL Monitoring.**

![84349-203grr0ds0y.png](https://imgs.gnux.cn/usr/uploads/2019/01/451210982.png)