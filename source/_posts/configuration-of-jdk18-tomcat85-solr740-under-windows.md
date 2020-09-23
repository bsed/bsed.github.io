---
title: windows下jdk1.8+tomcat8.5+solr-7.4.0配置
date: 2018-09-06 06:08:00
updated: 2018-12-27 08:49:46
tags: 
- android
- root
categories: 
- android

---
Apache Solr是一个功能强大的搜索服务器，它支持REST风格API。Solr是基于Lucene的，Lucene 支持强大的匹配能力，如短语，通配符，连接，分组和更多不同的数据类型。它使用 Apache Zookeeper特别针对高流量进行优化。

一. 安装软件

1.jdk8配置 [jdk链接](https://jingyan.baidu.com/article/f96699bbd30ca8894e3c1bdb.html)

2.tomcat8.5官网下载 [点击打开链接](http://mirrors.shu.edu.cn/apache/tomcat/tomcat-8/v8.5.33/bin/apache-tomcat-8.5.33-windows-x64.zip)

3.solr7.2.1官方下载 [点击打开链接](http://mirrors.shu.edu.cn/apache/lucene/solr/7.4.0/solr-7.4.0.zip)


<!--more-->


二. 安装solr

1. 将 solr-7.4.0.zip 目录下的 `C:\Users\Administrator\Downloads\solr-7.4.0\server\solr-webapp` 的 webapp复制到
`C:\tomcat8\webapps` 目录下重命名solr

![35324-7gxnufxp4iw.png](https://imgs.gnux.cn/usr/uploads/2018/09/916767948.png)

2. 将solr-7.4.0目录下的 `C:\Users\Administrator\Downloads\solr-7.4.0\server\lib\ext` 全部jar，和`C:\Users\Administrator\Downloads\solr-7.4.0\server\lib` 以metrics开头的jar和gmetric4j-1.0.7.jar
和 `C:\Users\Administrator\Downloads\solr-7.4.0\dist` 复制到 `C:\tomcat8\webapps\solr\WEB-INF\lib`
如图：
![01854-50alianplld.png](https://imgs.gnux.cn/usr/uploads/2018/09/1074733009.png)
![33133-3pah0dkpsx7.png](https://imgs.gnux.cn/usr/uploads/2018/09/3869257433.png)
![87087-j0ecruk43go.png](https://imgs.gnux.cn/usr/uploads/2018/09/924080614.png)


3.在将 `C:\Users\Administrator\Downloads\solr-7.4.0\server\resources` 的log4j.properties

复制到 `C:\tomcat8\webapps\solr\WEB-INF\classes` 下

配置日志路径：
将 `C:\tomcat8\bin` 下的catalina.bat 打开
添加 set "JAVA_OPTS=%JAVA_OPTS% -Dsolr.log.dir=../logs"
如图：
![80154-lr6k2t0ce6.png](https://imgs.gnux.cn/usr/uploads/2018/09/1412848308.png)

4. 在 `C:\tomcat8` 新建solr_home将 `C:\Users\Administrator\Downloads\solr-7.4.0` 下的contrib和dist复制进去

再将 `C:\Users\Administrator\Downloads\solr-7.4.0\server` 下的solr复制到solr_home下重命名solrhome


修改 `C:\tomcat8\solr_home\solrhome\configsets\_default\conf` 下的solrconfig.xml

只留一个../

修改后如图：
![64840-g3rxa4slb1p.png](https://imgs.gnux.cn/usr/uploads/2018/09/950380277.png)

5. 打开 `C:\tomcat8\webapps\solr\WEB-INF`

修改xml将env-entry注释打开配置成如下：
```xml
<env-entry>
       <env-entry-name>solr/home</env-entry-name>
       <env-entry-value>../solr_home/solrhome</env-entry-value>
       <env-entry-type>java.lang.String</env-entry-type>
</env-entry>

<!-- <security-constraint> -->
    <!-- <web-resource-collection> -->
      <!-- <web-resource-name>Disable TRACE</web-resource-name> -->
      <!-- <url-pattern>/</url-pattern> -->
      <!-- <http-method>TRACE</http-method> -->
    <!-- </web-resource-collection> -->
    <!-- <auth-constraint/> -->
  <!-- </security-constraint> -->
  <!-- <security-constraint> -->
    <!-- <web-resource-collection> -->
      <!-- <web-resource-name>Enable everything but TRACE</web-resource-name> -->
      <!-- <url-pattern>/</url-pattern> -->
      <!-- <http-method-omission>TRACE</http-method-omission> -->
    <!-- </web-resource-collection> -->
  <!-- </security-constraint> -->
```
如图：
![87807-dwvzxyckxj.png](https://imgs.gnux.cn/usr/uploads/2018/09/2702397664.png)
![07645-0g5g7iidqew4.png](https://imgs.gnux.cn/usr/uploads/2018/09/2940786921.png)

6. 如果tomcat还有其他服务配置 `C:\tomcat8\conf` server.xml
```
<Context path="/solr/" docBase="../webapps/solr" reloadable="false" crossContext="true">
			<Environment name="solr/home" type="java.lang.String" value="../solr_home/solrhome" override="true"/>
</Context>
```
如图：
![98011-5td67v13d5e.png](https://imgs.gnux.cn/usr/uploads/2018/09/3837097183.png)

../solr_home/solrhome 必须使用相对路径
7. 启动服务

点击 `C:\tomcat8\bin` 下的 `startup.bat`

打开浏览器输入[http://localhost:8080/solr/index.html#/](http://localhost:8080/solr/index.html#/)访问就可以了