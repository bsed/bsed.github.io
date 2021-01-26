---
title: "flume日志导入elasticsearch"
categories: [ "Linux" ]
tags: [ "elasticsearch","flume" ]
draft: false
slug: "flume-elasticsearch"
date: "2014-12-08 18:17:00"
---

## 简介 ##

搭建Flume + ElasticSearch + kibana 快速搭建实时日志查询、收集与分析系统


<!--more-->


## flume配置 ##

    agent.sources = tail
    agent.channels = memoryChannel
    agent.channels.memoryChannel.type = memory
    agent.sources.tail.channels = memoryChannel
    agent.sources.tail.type = exec
    agent.sources.tail.command = tail -F /root/test.log
    agent.sources.tail.interceptors=i1 i2 i3
    agent.sources.tail.interceptors.i1.type=regex_extractor
    agent.sources.tail.interceptors.i1.regex = (\\w.*):(\\w.*):(\\w.*)\\s
    agent.sources.tail.interceptors.i1.serializers = s1 s2 s3
    agent.sources.tail.interceptors.i1.serializers.s1.name = source
    agent.sources.tail.interceptors.i1.serializers.s2.name = type
    agent.sources.tail.interceptors.i1.serializers.s3.name = src_path
    agent.sources.tail.interceptors.i2.type=org.apache.flume.interceptor.TimestampInterceptor$Builder
    agent.sources.tail.interceptors.i3.type=org.apache.flume.interceptor.HostInterceptor$Builder
    agent.sources.tail.interceptors.i3.hostHeader = host
    agent.sinks = elasticsearch
    agent.sinks.elasticsearch.channel = memoryChannel
    agent.sinks.elasticsearch.type=org.apache.flume.sink.elasticsearch.ElasticSearchSink
    agent.sinks.elasticsearch.batchSize=100
    agent.sinks.elasticsearch.hostNames=127.0.0.1:9300
    agent.sinks.k1.indexType = bar_type
    agent.sinks.elasticsearch.indexName=logstash
    agent.sinks.elasticsearch.clusterName=elasticsearch
    agent.sinks.elasticsearch.serializer=org.apache.flume.sink.elasticsearch.ElasticSearchLogStashEventSerializer

## 测试数据 /root/test. ##

    website:weblog:login_page weblog data1
    website:weblog:profile_page weblog data2
    website:weblog:transaction_page weblog data3
    website:weblog:docs_page weblog data4
    syslog:syslog:sysloggroup syslog data1
    syslog:syslog:sysloggroup syslog data2
    syslog:syslog:sysloggroup syslog data3
    syslog:syslog:sysloggroup syslog data4
    syslog:syslog:sysloggroup syslog data5
    syslog:syslog:sysloggroup syslog data6

## flume环境配置 ##

    FLUME_CLASSPATH="/root/ray/elasticsearch-1.4.0/lib/*"
    //配置flume依赖的elasticsearch classpath，否则会报:
    //flume Caused by: java.lang.ClassNotFoundException: org.elasticsearch.common.transport.TransportAddress

## flume启动 ##

    ../bin/flume-ng agent -c . -f es_log.conf -n agent -Dflume.root.logger=INFO,console

## es数据预览  ##
![1204303105.jpg][1]
## 扩展  ##
同时可以参考 ElasticSearch和kibana结合
搭建Flume + ElasticSearch + kibana 快速搭建实时日志查询、收集与分析系统

参考:

[How To Configure Elasticsearch on Hadoop with HDP](http://zh.hortonworks.com/blog/configure-elastic-search-hadoop-hdp-2-0/)


  [1]: https://imgs.gnux.cn/usr/uploads/2014/12/448390422.jpg