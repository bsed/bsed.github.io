---
title: JBoss 的配置
date: 2014-06-29 22:42:00
updated: 2016-06-10 23:40:49
tags: 
- nodejs
- javascript
- npm
- zcfy
- lodash
categories: 
- js

---
日志文件设置
若需要修改JBoss默认的log4j设置，可修改JBoss安装目录”server”default”conf下的jboss-log4j.xml文件，在该文件中可以看到，log4j的日志输出在JBoss安装目录”server”default”log下的server.log文件中。对于log4j的设置，读者可以在网上搜索更加详细的信息。

web 服务的端口号的修改
这点在前文中有所提及，即修改JBoss安装目录”server”default”deploy”jboss-web.deployer下的server.xml文件，内容如下：

<connector port=“8080” address=“${jboss.bind.address}”

maxThreads=“250” maxHttpHeaderSize=“8192”

emptySessionPath=“true” protocol=“HTTP/1.1”

enableLookups=“false” redirectPort=“8443” acceptCount=“100”

connectionTimeout=“20000” disableUploadTimeout=“true” />

将上面的8080端口修改为你想要的端口即可。重新启动JBoss后访问：http://localhost/:%E6%96%B0%E8%AE%BE%E7%BD%AE%E7%9A%84%E7%AB%AF%E5%8F%A3%EF%BC%8C%E5%8F%AF%E7%9C%8B%E5%88%B0JBoss%E7%9A%84%E6%AC%A2%E8%BF%8E%E7%95%8C%E9%9D%A2%E3%80%82

要使用IP访问，修改address=“${jboss.bind.address}” 为 address=“${0.0.0.0}”

JBoss 的安全设置
1） jmx-console 登录的用户名和密码设置

默认情况访问 http://localhost:8080/jmx-console 就可以浏览jboss的部署管理的一些信息，不需要输入用户名和密码，使用起来有点安全隐患。下面我们针对此问题对jboss进行配置，使得访问jmx-console也必须要知道用户名和密码才可进去访问。步骤如下：

i) 找到JBoss安装目录/server/default/deploy/jmx-console.war/WEB-INF/jboss-web.xml文件，去掉<security-domain>java:/jaas/jmx-console</security-domain>的注释。修改后的该文件内容为：

<jboss-web>