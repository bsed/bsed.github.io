---
title: "Spring 集成 Hibernate4.3.X platform.spi.JtaPlatform异常"
categories: [ "Java" ]
tags: [ "hibernate","spring" ]
draft: false
slug: "spring-integrated-orghibernateservicejtaplatformspijtaplatform-hibernate43x-exception"
date: "2015-09-20 17:14:00"
---

使用Spring3.2.4集成Hibernate4.3.5时，出现以下异常

Causedby:java.lang.ClassNotFoundException:org.hibernate.service.jta.platform.spi.JtaPlatform

原因是在Hibernate4.3.X中，org.hibernate.service.jta.platform.spi.JtaPlatform类换成了：

org.hibernate.engine.transaction.jta.platform.spi.JtaPlatform;

 


<!--more-->


*解决方案：*

Hibernate用4.2.X及以下版本  或 Spring用4.0及以上版本

经过升级，Spring4.0.3，Hibernate4.3.5后，解决！

原文:http://www.cnblogs.com/leiOOlei/p/3668229.html