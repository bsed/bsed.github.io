---
title: 解决Url带中文参数乱码问题
date: 2014-06-02 22:06:00
updated: 2017-02-06 22:11:21
tags: 
- docker
- swarm
categories: 
- go

---
介绍下如何配置Tomcat 来解决Url带中文参数乱码问题；

首先打开Tomcat安装目录，以Tomcat7为例，其他版本基本一样；

打开conf文件
![apache-tomcat7.0.63.jpg][1]

打开`server.xml`


<!--more-->


![apache-tomcat7_02.jpg][2]


大概在70行左右的位置 我们加一个属性 `URIEncoding="UTF-8"`
![apache-tomcat7_03.jpg][3]

搞成这样即可。。这个是发布项目的时候，配置方式。


下面介绍开发的时候，如何配置：

打开eclipse

找到server

![apache-tomcat7_04.jpg][4]

点开`server.xml`
![apache-tomcat7_05.jpg][5]

和刚才一样，加下 `URIEncoding="UTF-8"` 这样就支持Url中文参数了；


  [1]: https://imgs.gnux.cn/usr/uploads/2017/02/3106452532.jpg
  [2]: https://imgs.gnux.cn/usr/uploads/2017/02/2126629989.jpg
  [3]: https://imgs.gnux.cn/usr/uploads/2017/02/2013501410.jpg
  [4]: https://imgs.gnux.cn/usr/uploads/2017/02/117221607.jpg
  [5]: https://imgs.gnux.cn/usr/uploads/2017/02/3500230955.jpg