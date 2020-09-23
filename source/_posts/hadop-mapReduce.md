---
title: [转]Hadoop之MapReduce快速入门
date: 2014-12-25 11:12:00
updated: 2014-12-25 14:29:35
tags: 
- php
categories: 
- php

---
最近有需要分析某个省份的的dns日志，从中解析出一段时间top100的域名排行，日志量一天大概有3、4G的样子，数据量还是很客观。好消息是公司已经有了完整的hadoop集群，借助hadoop分析还是很方便的，坏处是我一点都不懂hadoop,囧个。不过之前写过类似的分析dns日志脚步算是有点经验了，总之硬着头皮上吧，开搞。

1.hadoop基本操作

磨刀不误砍柴工，纵身登上hadoop,快速的敲了个history偷看了下同事的操作记录，看到了最常用的几条指令，心里有底了。区区3、4条命令，小样，我真不信我搞不了你。

    hadoop fs -ls /user/hadoop/
    
    hadoop fs -rmr /user/hadoop/sicong
    
    hadoop fs -get /user/hadoop/sicong/part-00000 ./result/

大致有如上几条，稍微解释下：


<!--more-->


a)以上所有指令都是使用hadoop用户执行的，so首先需要切换到hadoop用户 su – hadoop,我第一次就不知道土鳖了一把

b)hadoop 所有的文件系统操作都是以hadoop fs 开头的，hadoop fs -ls 即对执行目录执行ls操作，作用等同Linux下的ls，hadoop fs -rmr 删除执行的目录，很危险，千万慎重

c)hadoop采用的是分布式的HDFS，彼此节点可以相互通信，因为登录上hadoop集群任一节点执行的操作都会在全集群执行的,so 你懂的，小心～

2.MapReduce简介：

前面稍微介绍了基本的hadoop操作，下面要说的就是大名鼎鼎的MapReduce.

1.什么是mapreduce:

mapreduce是hadoop最小的工作单元，其实分为两个处理阶段，map阶段、reduce阶段，当然中间可能还有Shuffle阶段。每个阶段都是以键/值的形式作为输入，输出，同时hadoop的streaming采用了系统标准的输入、输出，更方便我们的使用，可以使用任何语言编写map脚本、reduce脚本。

2.为什么要使用mapreduce：

小规模的数据处理我们当然可以使用系统的awk、python脚本等进行处理的，首先我们就用python脚本处理。具体的Python脚本以及脚本思路参加前面文章：python脚本分析dns日志获取域名排行。

但可以看出前篇文章的脚本程序、原始数据都是在本机的，这个脚本在处理小规模数据（百M级别)的毫无问题,但遇到大规律的数据就无能为力了，受制于单机的计算能力、内存、存储。当数据规模达到T级别的时候本地的存储就无法承受了，数据会分别在不同的机器甚至机架上，前面的脚本就只能利用本地存储、本机内存，根本没办法处理。

hadoop基于HDFS的分布式存储将数据存储到大量的hadoop节点上，处理的时候目标数据会被拆分了等长的小数据块（分片），同时在不同的节点启动任务处理相应的数据块，之后排序，将结果合并、汇总，交由reduce处理。这个过程中充分利用了集群的计算能力、内存，同时将数据拆分，由统一的存储、任务调度等，so这就是mapreduce的意义。

3.mapreduce数据流：
![mapreduce数据流1-300x168.jpg][1]
上图是hadoop权威指南的一个插图，较好的解释了map阶段、reduce阶段的数据流向。

3.必要的准备：

开始动手之前大致说下必要的准备，首先建立一个目录，在该目录下存放你相应的目标样本文件，以及相应的mapper.py 、reducer.py等。

    [hadoop@CXN-HX-x-3x1 sicong]$ ls
    domain.py  log  mapper.py  queries.log.CMN-CQ.20130830031330.gz  reducer.py  reducer.py.bak  result  startup.sh

我的目录结构如上，queries.log.CMN-CQ.20130830031330.gz为目标文件的一个样本，用来测试的，result用以存储最后的输出结果。

startup.sh是抄了同事(xdays，大神一枚)的用以简化hadoop的操作：

    [hadoop@CXN-HX-x-3x1 sicong]$ cat startup.sh
    
    #!/bin/sh
    
    wd=/home/hadoop/sicong/
    
    # 1. get raw result sorted by domain and count
    hadoop jar \
    $HADOOP_HOME/contrib/streaming/hadoop-streaming-1.0.3.jar \
    -file ${wd}mapper.py -mapper ${wd}mapper.py \
    -file ${wd}reducer.py -reducer ${wd}reducer.py \
    -input /user/hadoop/fdns_logs/08/30/CMN-CQ-2-*  \
    -output /user/hadoop/sicong/  \
    -jobconf stream.non.zero.exit.is.failure=false \
    -jobconf stream.recordreader.compression=gzip \
    -jobconf mapred.map.max.attempts=”3″ \
    -jobconf mapred.reduce.max.attempts=”3″ \
    -jobconf mapred.max.map.failures.percent=”1″

wd参数为你建立的目录，-input 执行的你目标数据文件，-output 指定数据结果存储路径，咳咳其实这段解释也是抄同事的。但需要注意的是这里的input、output都是hadoop的目录。

4.编写mapreduce

    [hadoop@CXN-HX-1-3x1 sicong]$ cat mapper.py
    [sourcecode language="python"]
    #!/usr/bin/env python
    import sys
    for line in sys.stdin:
    line = line.strip()
    segs = line.split(‘|’)
    print ‘%s’ % segs[2]
    
    [hadoop@CXN-HX-1-3x1 sicong]$ cat reducer.py
    #!/usr/bin/env python
    
    from operator import itemgetter
    import sys
    
    domain_list = {}
    
    for key in sys.stdin:
    key = key.strip()
    
    if key in domain_list:
    domain_list[key] += 1
    else:
    domain_list[key] = 1
    
    count = 0
    for v in sorted(domain_list.iteritems(),key =lambda x:x[1],reverse=True):
    print v[1],v[0]
    #display the top 100
    if count > 100:
    break
    count += 1

可以看出没什么特别的，只是将前面的domain.py文件拆分了，将数据提前的部分拆分到map.py中，将数据排序、计算的部分拆分到reduce.py，当然其实也可以不拆分，在一个脚本里面写了，只是基于性能的问题拆分了。

5.测试mapreduce：

写好了别着急跑脚本，最好先测试下，明白了hadoop的mapreduce过程就可以用如下命令去模仿mapreduce的流程去测试下：
![test-mapreduce.png][2]
如果执行成功了，则表示mapreduce脚本无大的问题，如果失败，那。。你就慢慢该脚本吧。

5.执行

真到可以其实就很简单了，直接拍下脚本就好了，sh startup.sh 就好了。

最后别忘了把输出结果（默认为part-00000)取回来，因为output指定的目录为hadoop的目录，因此需要将结果从hadoop中拷贝到系统目录下（我保存在3中目录下的result下）：

`hadoop dfs -get  /user/hadoop/sicong/part-00000   ./result/`

6.总结：
mapreduce不难写，难在写好一个mapreduce，如果中间采用了正则匹配等很耗时间的操作，那很有可能mapreduce会跑失败。同样这个脚步也有很多可以优化的地方，例如最后的reduce阶段可以不排序直接输出，然后进入下一个mapreduce使用hadoop进行排序；或者每次map阶段之后就可以进行计数、排序之后再进入reduce阶段。总之尽可能的拆分任务，加快任务的处理。


  [1]: https://imgs.gnux.cn/usr/uploads/2014/12/881910644.jpg
  [2]: https://imgs.gnux.cn/usr/uploads/2014/12/2608033351.png