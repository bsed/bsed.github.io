---
title: "EJB与JAVA BEAN的区别"
categories: [ "Java" ]
tags: [ "EJB","java bean" ]
draft: false
slug: "the-difference-between-ejb-and-java-bean"
date: "2013-09-10 10:40:00"
---

Java Bean 是可复用的组件，对Java Bean并没有严格的规范，理论上讲，任何一个Java类都可以是一个Bean。但通常情况下，由于Java Bean是被容器所创建（如Tomcat）的，所以Java Bean应具有一个无参的构造器，另外，通常Java Bean还要实现Serializable接口用于实现Bean的持久性。

Java Bean实际上相当于微软COM模型中的本地进程内`COM`组件，它是不能被跨进程访问的。Enterprise Java Bean 相当于`DCOM`，即分布式组件。它是基于Java的远程方法调用（RMI）技术的，所以EJB可以被远程访问（跨进程、跨计算机）。但EJB必须被布署在诸如Webspere、WebLogic这样的容器中，EJB客户从不直接访问真正的EJB组件，而是通过其容器访问。EJB容器是EJB组件的代理，EJB组件由容器所创建和管理。客户通过容器来访问真正的EJB组件。