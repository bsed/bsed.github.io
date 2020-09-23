---
title: memcached的安装，包括依赖libevent和repcached
date: 2011-10-19 21:25:00
updated: 2015-10-19 21:26:14
tags: 
- yum
categories: 
- linux

---
> 安装顺序：

	1. 先安装libevent，因为memcached依赖它；
	2. 给memcached打上repcached补丁，支持主从结构，实现高可用；
	3. 安装memcached，启用replication；

	
## 1. libevent
	
	# wget https://github.com/downloads/libevent/libevent/libevent-2.0.21-stable.tar.gz -P /opt/softs/
	# ./configure --prefix=/usr/local/
	# make && make install


## 2. repcached && memcached

当前补丁最新位1.4.13，memcached的版本应该一直，否则会出现异常；

	# wget https://memcached.googlecode.com/files/memcached-1.4.13.tar.gz -P /opt/softs/
	# wget http://mdounin.ru/files/repcached-2.3.1-1.4.13.patch.gz -P ./

	# tar zxvf memcached-1.4.13.tar.gz -C /opt/softs/
	# gzip -d repcached-2.3.1-1.4.13.patch.gz

	# cd /opt/softs/memcached-1.4.13
	# patch -p1 -i ../repcached-2.3.1-1.4.13.patch
	# ./configure --prefix=/usr/local/ --with-libevent=/usr/local/ --enable-replication
	# make && make install


## 3. memcached (with repcached) 主从测试

在本机的两个不同的端口上启动两个memcached实例，一主一从，然后测试相互之间的数据同步。
memcached主从测试在本机的两个不同的端口上启动两个memcached实例，一主一从，然后测试相互之间的数据同步。

### 3.1 启动两个memcached实例

	# memcached -d -m -p 11211 -u nobody -l 127.0.0.1 -x 127.0.0.1 -X 11222 -P /tmp/localhost_master.pid -vv
	# memcached -d -m -p 11212 -u nobody -l 127.0.0.1 -x 127.0.0.1 -X 11222 -P /tmp/localhost_slave.pid -vv

> 两个实例的端口分别为11211和11212，共同的监听端口为11222。

### 3.2 通过telnet测试数据的同步

> 进入master，set两个值

	[root@localhost ~]# telnet 127.0.0.1 11211
	Trying 127.0.0.1...
	<31 new auto-negotiating client connection
	Connected to 127.0.0.1.
	Escape character is '^]'.
	set city 0 0 7
	31: Client using the ascii protocol
	<31 set city 0 0 7
	tianjin
	>31 STORED
	replication: pop
	replication: pop
	STORED
	set city2 0 0 7
	<31 set city2 0 0 7
	beijing
	>31 STORED
	replication: pop
	replication: pop
	STORED	


<!--more-->


> 进入slave，get刚才设置的值，并删除其中一个值

	[root@localhost ~]# telnet 127.0.0.1 11212
	Trying 127.0.0.1...
	Connected to 127.0.0.1.
	Escape character is '^]'.
	get city
	VALUE city 0 7
	tianjin
	END
	get city2
	VALUE city2 0 7
	beijing
	END
	delete city2
	<26 delete city2
	REP>26 DELETED
	DELETED

> 再次进入master，获取set的两个值，删除的数据是否可以get

	[root@localhost ~]# telnet 127.0.0.1 11211
	Trying 127.0.0.1...
	<31 new auto-negotiating client connection
	Connected to 127.0.0.1.
	Escape character is '^]'.
	get city
	31: Client using the ascii protocol
	<31 get city
	>31 sending key city
	>31 END
	VALUE city 0 7
	tianjin
	END
	get city2
	<31 get city2
	>31 END
	END
