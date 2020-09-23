---
title: 分布式搜索引擎ElasticSearch的安装，插件安装以及配置说明
date: 2015-10-19 21:31:00
updated: 2015-10-19 21:32:57
tags: 
- js
- window-open
categories: 
- js

---
## 1. 安装与启动

### 1.1 安装ElasticSearch

> 两种安装方式，一种是根据发行版，通过ElasticSearch提供的repositories安装，参考[官方指南](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/setup-repositories.html); 另一种是利用压缩包安装；由于第二种方式，在配置上更加灵活，这里主要介绍第二种方式的安装和配置。


从[ElasticSearch网站](http://www.elasticsearch.org/overview/elkdownloads/)下载压缩包：`elasticsearch-1.0.0.tar.gz`，解压到指定目录：


<!--more-->


	$ wget https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-1.0.0.tar.gz -P /opt/app
	$ cd /opt/app
	$ tar zxvf elasticsearch-1.0.0.tar.gz -C ./

> 启动ElasticSearch很简单，执行安装目录下bin/elasticsearch即可；ElasticSearch 1.0.0的启动机制有一些变化，默认在前台运行，-d参数表示在后台运行；另外还提供-p参数，后接文件名，保存当前ElasticSearch进程的pid，方便关闭进程；

	$ bin/elasticsearch -d -p /tmp/es.pid
	$ kill `cat /tmp/es.pid`

> 另外还有一种启动方式，通过serviceWrapper，可以更加方便地控制ElasticSearch的启动、关闭以及重启，在下一节`安装插件`中介绍。

### 1.2 安装插件

ElasticSearch官方和社区提供了很多插件用于将集群和节点的状态可视化。推荐安装的插件有：elasticsearch-head， marvel，bigdesk，servicewrapper

	// elasticsearch-head的安装与访问
	# bin/plugin --install mobz/elasticsearch-head
	http://localhost:9200/_plugin/head/

	// marvel的安装与访问
	# bin/plugin -i elasticsearch/marvel/latest
    http://localhost:9200/_plugin/marvel

	// bigdesk的安装与访问（当前不支持ElasticSearch 1.0.0）
	# bin/plugin --install lukas-vlcek/bigdesk
	http://localhost:9200/_plugin/bigdesk/

> 下面介绍servicewrapper的使用：从[该链接](https://github.com/elasticsearch/elasticsearch-servicewrapper)下载，解压后将service文件夹放到ElasticSearch安装目录下的bin目录下。

	$ wget https://github.com/elasticsearch/elasticsearch-servicewrapper/archive/master.zip -P /opt/app
	$ cd /opt/app
	$ unzip elasticsearch-servicewrapper-master -d ./
	$ mv elasticsearch-servicewrapper-master/service elasticsearch-1.0.0/bin/

> 配置: 编辑elasticsearch.conf文件，主要是配置前两行：ES_HOME及ES_HEAP_SIZE

	$ vim bin/service/elasticsearch.conf
	// set.default.ES_HOME=/data/app/elasticsearch-1.0.0
	// set.default.ES_HEAP_SIZE=2048

> 用法：bin/service/elasticsearch start | stop | restart | install | remove | console | condrestart | status

	$ bin/service/elasticsearch start

## 2. 配置

> 配置文件为{ES_HOME}/config目录下的elasticsearch.yml，主要对集群、节点、索引（分片和副本）以及持久化和集群发现机制等进行参数设置。这里对一些常见的配置进行简单的说明和示例，配置文件的详细解释和说明会单独写一篇文章。

	// 集群的名称，用于集群内节点的自动发现
	cluster.name: es-test

	// 节点的名称，标识一个节点
	node.name: node_79_75

	// 可以被选为主节点，同时可以存储数据
	node.master: true
	node.data: true

	//节点的分片和副本的数量
	index.number_of_shards: 3
	index.number_of_replicas: 1

	// 配置文件、索引数据以及日志和插件等的目录，如果不存在，新建即可
	path.conf: /data/app/elasticsearch-1.0.0/config
	path.data: /data/app/elasticsearch-1.0.0/data
	path.logs: /data/app/elasticsearch-1.0.0/logs
	path.plugins: /data/app/elasticsearch-1.0.0/plugins

	// 锁定内存，防止内存交换影响ES性能
	bootstrap.mlockall: true

	// 持久化策略，本次存储
	gateway.type: local

	// 启用单播，显式指定节点的发现，当节点不在同一网段，无法自动发现时，启动单播，指定要发现的节点列表
	// 比如当前节点的ip为10.10.79.75，与另两个节点不在同一网段，使用单播机制
	discovery.zen.ping.multicast.enabled: false
	discovery.zen.ping.unicast.hosts: ["10.11.52.131:9300", "10.11.52.134:9300"]

### 参考

+ [ElasticSearch reference](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/index.html)
+ [ElasticSearch配置文件详解](http://www.searchtech.pro/articles/2013/02/18/1361194291548.html)
