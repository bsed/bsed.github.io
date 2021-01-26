---
title: "memcached的基本用法，参数和命令详解"
categories: [ "PHP" ]
tags: [ "memcached" ]
draft: false
slug: "the-basic-usage-of-memcached-parameter-and-command"
date: "2012-10-19 21:26:00"
---

## 1. memcached 参数说明：
	
	# memcached -h

### 1.1 memcached 的参数

常用参数

	-p <num> 监听的TCP端口号，默认是11211；（port）
	-l <addr> 监听的主机地址，默认是INADDR_ANY，即所有地址，<addr>可以是host:port的形式，如果没有指定port，则使用-p或者-U的值；可以指定多个地址，以逗号分隔或者多次使用-l参数；尽量不要使用默认值，有安全隐患。(listen)
	-d 以守护进程运行 (daemon)
	-u <username> 指定进程的所有者（只有以root用户执行时才可以使用该参数）(username)
	-m <num> 用于存储数据的最大内存，单位是MB，默认是64MB；(memory)
	-c <num> 最大并发连接数，默认是1024；
	-vv 显示更详细的信息（还显示客户端的命令和响应）
	-vvv 显示最详细的信息（还显示内部的状态转变）
	-h 显示帮助信息
	-P <file> 将PID保存到<file>中，仅和-d参数一起使用；
	-f <factor> chunk的增幅因子，默认是1.25，不同的slab class，slab page大小相同，但是chunk大小不等，chunk的大小根据这个增幅因子增长；(factor)
	-n <bytes> 为key+value+flags分配的最小内存，单位bytes，默认是48；chunk数据结构本身要占据48字节，所以实际大小是n+48；
	-t <num> 使用多少个线程，默认是4；（thread）
	-I 设置slab page的大小，即设置可以保存的item的最大值，默认1MB，最小是1K，最大值128M；


<!--more-->


其它参数

	-U <num> 监听的UDP端口号，默认是11211，0表示关闭UDP监听；（UDP）
	-s <file> 要监听的UNIX socket路径（禁用网络支持）（socket)
	-a <mask> UNIX socket的访问掩码（access mask），八进制表示，默认是0700. (mask)
	-r 文件数量的最大值 (rlimit)
	-M 内存耗尽时返回错误，而不是通过LRU淘汰内容；
	-k 锁定所有页内存；允许被锁定的内存是有限制的，超过限制可能会失败。
	-v 显示启动信息（错误和警告信息）(verbose)
	-i 显示memcached和libevent的licence信息
	-L 一次申请大的内存页（如果可以）；增大内存页的大小，可以提高性能；
	-D <char> 指定key前缀与ID的分隔符，用于stats信息显示，默认是冒号：，如果使用了该参数，则stats收集自动启用了，否则，需要发送命令“stats detail on”命令来启动stats的收集。
	-R 每一个事件（event）的最大请求数，限制最大请求数可以防止线程饥饿，默认是20；
	-C 禁用CAS；
	-b 设置backlog队列限制，默认1024；
	-B 指定绑定协议，ascii，binary或者auto，其中auto是默认值；

### 1.2 repcached的参数：

	-x <ip_addr> peer主机的主机名或者ip地址；
	-X peer主机的TCP端口，即主从同步端口，共同的监听端口

### 1.3 常用的参数组合

	# memcached -d -m -p 11212 -u nobody -l 127.0.0.1 -x 127.0.0.1 -X 11222 -P /tmp/localhost_slave.pid -vv


## 2. 基本命令与操作
	
### 2.1 存储的命令

主要有：set，add，replace，append，prepend，cas；格式为：

	command key flag expiration_time bytes
	value

> key表示键，flag表示key/value的额外信息，expiration_time表示过期时间，单位为秒，0表示永不过期，bytes表示值所占的字节数，必须完全匹配，value表示key对应的值，总是出现在第二行。

> set命令表示存储一个key/value对，如果该key已存在，则更新对应的value值；如果成功，返回STORED。

	set file_path 0 0 5
	/opt/
	<29 rep file_path 0 0 5 12
	REP>29 STORED
	STORED

> add命令也表示增加key/value，如果key/value已存在，add操作失败；保存成功返回STORED，失败返回NOT_STORED。

	add file_path 0 60 5
	/opt/
	NOT_STORED
	add file_suffix 0 0 2
	js
	<29 rep file_suffix 0 0 2 16
	REP>29 STORED
	STORED

> replace命令表示更新key对应的value值，如果key/value不存在，replace操作失败；成功返回STORED，失败返回NOT_STORED；

	replace first 0 0 7
	tianjin
	STORED
	replace second 0 0 8
	shanghai
	NOT_STORED

> append表示在key对应的value值后追加数据，key必须已存在，否则操作失败；成功返回STORED，失败返回NOT_STORED；

	append second 0 0 8
	shanghai
	NOT_STORED
	append first 0 0 3
	 go
	STORED

> prepend在key对应的value值的前面追加数据，key必须已存在，否则操作失败；成功返回STORED，失败返回NOT_STORED；

	prepend second 0 0 2
	hi
	NOT_STORED
	prepend first 0 0 2
	hi
	STORED
	get first
	VALUE first 0 15
	hitianjin go go
	END

> cas (check and set)：先比较后存储，即原子更新，原理类似于乐观所。每次请求存储某个数据时附带一个cas值，memcached比对这个cas值与当前存储数据的cas值是否相等，如果相等，则更新数据，否则操作失败；当前存储的cas值通过gets命令获取。成功返回STORED，失败返回EXISTS。

	gets first
	VALUE first 0 7 12		// 12表示cas id，可以理解为版本号
	chengdu
	END
	cas first 0 0 8 10		// 10 != 12，cas失败
	shanghai
	EXISTS
	cas first 0 0 8 12		// 12表示gets后没有修改key的值，因此可以set
	shanghai
	STORED

### 2.2 读取的命令

> get根据key获取value值；可以获取多个key的值；get key | get key1 key2 

	get first
	VALUE first 0 8
	shanghai
	END

	get first fine
	VALUE first 0 8
	shanghai
	VALUE fine 0 5
	yes!!
	END

> gets是与cas一起使用的命令，gets会额外返回一个cas值，可以理解为版本；如果最后一次gets后，该cas值改变了，则cas设置的值不会存储；gets key | gets key1 key2

	gets first
	VALUE first 0 8 13
	shanghai
	END
	set first 0 0 7
	chengdu
	STORED
	cas first 0 0 7 13		// 因为gets后set了，所以cas id改变了，cas失败
	chengdu
	EXISTS

> delete命令删除key/value对，一次只能删除一个key/value对；如果要删除的key不存在，操作失败: delete key

	delete first second
	CLIENT_ERROR bad command line format.  Usage: delete <key> [noreply]
	delete first
	DELETED

> incr/decr: 如果key的value值表示的是一个64位整数，可以通过incr和decr命令进行数值的增减: incr/decr key num

	set id 0 120 2
	10
	STORED
	incr id 10
	20
	decr id 5
	15

### 2.3 统计的命令

> stats显示进程及当前状态等信息。

	stats 
	STAT pid 1224			// 进程id
	STAT uptime 30385		// 系统运行的事件，单位：秒
	STAT time 1392199633	// 系统当前事件，Unix时间戳表示的时间：2/12/2014 6:25:40 PM
	STAT version 1.4.13			// memcached版本
	STAT libevent 2.0.21-stable	// libevent版本
	STAT pointer_size 64		// 操作系统字大小（64位）
	STAT rusage_user 1.892712	// 进程累计用户时间
	STAT rusage_system 0.996848	// 进程累计系统时间
	STAT curr_connections 8		// 当前打开的连接数
	STAT total_connections 9	// 曾打开的连接总数
	STAT connection_structures 9	// 服务器分配的连接结构数
	STAT reserved_fds 20
	STAT cmd_get 29				// 执行get命令的总数
	STAT cmd_set 29				// 执行set命令的总数
	STAT cmd_flush 2			// 执行flush_all命令的总数
	STAT cmd_touch 0			 
	STAT get_hits 14			
	STAT get_misses 15			
	STAT delete_misses 3		
	STAT delete_hits 3
	STAT incr_misses 0
	STAT incr_hits 4
	STAT decr_misses 0
	STAT decr_hits 2
	STAT cas_misses 1
	STAT cas_hits 2
	STAT cas_badval 2
	STAT touch_hits 0
	STAT touch_misses 0
	STAT auth_cmds 0
	STAT auth_errors 0
	STAT bytes_read 1503
	STAT bytes_written 4125
	STAT limit_maxbytes 134217728
	STAT accepting_conns 1
	STAT listen_disabled_num 0
	STAT threads 4				// 线程数
	STAT conn_yields 0
	STAT hash_power_level 16
	STAT hash_bytes 524288
	STAT hash_is_expanding 0
	STAT expired_unfetched 0
	STAT evicted_unfetched 0
	STAT replication MASTER
	STAT repcached_version 2.3.1
	STAT repcached_qi_free 8191
	STAT bytes 217				// 存储的item字节数
	STAT curr_items 3			// 当前item数量
	STAT total_items 25			// item的总数
	STAT evictions 0			// 为获取空间删除的item数量
	STAT reclaimed 2
	END

> stats items 显示items的相关信息

	stats items
	STAT items:1:number 3
	STAT items:1:age 1552
	STAT items:1:evicted 0
	STAT items:1:evicted_nonzero 0
	STAT items:1:evicted_time 0
	STAT items:1:outofmemory 0
	STAT items:1:tailrepairs 0
	STAT items:1:reclaimed 2
	STAT items:1:expired_unfetched 0
	STAT items:1:evicted_unfetched 0
	END

> stats slabs 显示slab的相关信息

	stats slabs
	STAT 1:chunk_size 96
	STAT 1:chunks_per_page 10922
	STAT 1:total_pages 1
	STAT 1:total_chunks 10922
	STAT 1:used_chunks 3
	STAT 1:free_chunks 0
	STAT 1:free_chunks_end 10919
	STAT 1:mem_requested 217
	STAT 1:get_hits 14
	STAT 1:cmd_set 29
	STAT 1:delete_hits 3
	STAT 1:incr_hits 4
	STAT 1:decr_hits 2
	STAT 1:cas_hits 2
	STAT 1:cas_badval 2
	STAT 1:touch_hits 0
	STAT active_slabs 1
	STAT total_malloced 1048512
	END

> stats sizes 

	stats sizes
	STAT 96 3
	END

> stats cachedump 显示slab中items的信息(该命令在将来可能不再支持）：stats cachedump [slab id] [number of items, 0 for all items]

	set city 0 0 7
	tianjin
	STORED
	stats cachedump 1 0
	ITEM city [7 b; 1392169248 s]
	END
	get city
	VALUE city 0 7
	tianjin
	END

> flush_all 使cache中的所有items都过期，server不会停止，也不会刷新或者释放内存。

	flush_all
	OK
	get first
	END

### 参考

+ [memcached wiki](https://code.google.com/p/memcached/wiki/NewStart)
+ [memcached命令行操作](http://www.zrwm.com/?p=642)
+ [memcached命令行参数说明](http://blog.csdn.net/wanghai__/article/details/8539435)
+ [memcached详解](http://freeloda.blog.51cto.com/2033581/1289806)
