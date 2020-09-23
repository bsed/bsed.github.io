---
title:  web服务器压力测试一例
date: 2017-05-28 09:36:00
updated: 2017-05-28 09:36:24
tags: 
- mysql
categories: 
- sql

---
近期部门新上线一个服务，我们使用ab和locust分别测试

目前项目属于demo阶段，对访问量的支持不要求太高，我们暂且设定在500请求，20并发

# 工具介绍

### ab

ab全称为：Apache HTTP server benchmarking tool

ab是apache自带的压力测试工具。ab非常实用，它不仅可以对apache服务器进行网站访问压力测试，也可以对或其它类型的服务器进行压力测试。比如nginx、tomcat、IIS等。

### [locust](https://github.com/locustio/locust/)

locust是一个开源负载测试工具，支持python脚本。可仿真百万个用户

locust提供web ui界面，能够方便用户实时监控脚本运行状态。

# 开始测试

### 使用ab测试

假设我们的网站域名为：www.example.com

我们需要测试的页面为：`/index`


<!--more-->


```
ab -n 500 -c 20 https://www.example.com/index/  #500 访问，20 并发,－n表示请求数，－c表示并发数

```

之后会生成报表，如何阅读ab的报表，可以参考：[Web性能压力测试工具之ApacheBench（ab）详解](http://www.ha97.com/4617.html)

### 使用locust测试

locust是一个python库，我们可以使用pip安装：`pip install locustio`,查看帮助：`locust --help`

我们根据[官方教程](http://docs.locust.io/en/latest/)来写一个简单的测试

vim locustfile.py

```
from locust import Locust, TaskSet, task

class MyTaskSet(TaskSet):
    min_wait = 5000
    max_wait = 15000

    @task
    def task1(self):
        self.client.get("/index")
    '''
    @task
    def task2(self):
        self.client.get("/page2")
    '''

class MyLocust(Locust):
    task_set = MyTaskSet

```

locust -f locustfile.py --host=http://www.example.com

之后打开：127.0.0.1:8089,即可开始测试

![locust.png][1]
模拟登录的测试，下回有空再做.在locust中处理session很容易:

> The HttpSession instance will preserve cookies between requests so that it can be used to log in to websites and keep a session between requests.

# 参考

- [Web性能压力测试工具之ApacheBench（ab）详解](http://www.ha97.com/4617.html)
- [基于Locust、Tsung的百万并发秒杀压测案例](http://weibo.com/ttarticle/p/show?id=230940395887204907232)
- [Locust负载测试框架初探](http://blog.xuh.me/post/testing/locust-intro)
- [用locust+selenium压力测试登录后的页面](http://zhuhangyu.github.io/python/locust-for-login/)


  [1]: https://imgs.gnux.cn/usr/uploads/2017/05/2986507015.png

来源：http://blog.just4fun.site/stress-testing.html