---
title: Spring和Apache CXF,ActiveMQ整合记录
date: 2014-03-31 10:00:00
updated: 2015-07-28 10:03:23
tags: 
- htaccess
categories: 
- linux

---
Spring 3.2.8
Apache CXF 1.6
ActiveMQ 5.9.0
Tomcat 8.0.3
MyEclipse 2014
# 1.Spring需要的jar包：
spring-aop（aop支持）
spring-beans（配置spring bean必须的包）
spring-context（应用上下文支持，必须）
spring-core（spring核心的包，包括io操作，cglib等）
spring-experssion（可能是xml解析相关的包，不要的话XmlWebApplicationContext无法启动）
spring-jms（jms支持）
spring-web（在web容器中启动）
spring-tx（事务支持）
其中，spring-tx,spring-aop不是必选，根据需要。


<!--more-->


# 2. CXF需要的jar包：
cxf-api
cxf-rt-bindings-soap
cxf-rt-bindings-xml
cxf-rt-core
cxf-rt-databinding-jaxb
cxf-rt-frontend-jaxws
cxf-rt-frontend-simple
cxf-rt-transports-http
org-apache-servicemix-bundles-wsdl4j
org-apache.servicemix-specs-jaxws-api
stax2-api
woodstox-core-asl
xml-schema-core
asm(貌似需要到第三方下载)
cxf最坑爹。要么把所有jar包都加进去，肯定不出错，要么根据运行时的错误一个一个添加。
# 3. ActiveMQ
activemq-all
activemq-jms-pool
activemq-pool
commons-logging
commons-pool
其它的jar包也是根据需求添加。
## 1. Spring整合ActiveMQ
Spring有个JmsTemplate专门用于支持JMS。JmsTemplate虽然简化了JMS操作，但这个坑爹货每次发送消息时，默认都会建立新的connection，session和producer，并且发送完成后关闭。，这样的效率很低
如果不是使用的JCA容器来管理JMS连接，可以使用AMQ的poolingJMS连接。
首先，配置连接工厂：

    <bean id="jmsFactory" class="org.apache.activemq.pool.PooledConnectionFactory" destroy-method="stop">
    <property name="connectionFactory">
    <bean class="org.apache.activemq.ActiveMQConnectionFactory">
    <property name="brokerURL">
    <value>tcp://192.168.1.156:61616</value>
    </property>
    </bean>
    </property>
    </bean>

根据连接工厂配置jmsTemplate

    <!-- Spring JMS Template 线程安全，所以只需要配置一个实例-->
    <bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
    <property name="connectionFactory">
    <ref local="jmsFactory"/>
    </property>
    <!-- 启用QOS -->
    <property name="explicitQosEnabled"><value>1</value></property>
    <!-- 设置消息在broker中保存的最长时间-->
    <property name="timeToLive"><value>5000</value></property>
    <!-- 设置为NONPERSISTENT -->
    <property name="deliveryMode"><value>1</value></property>
    <!-- 接收数据的超时时间 -->
    <property name="receiveTimeout"><value>5000</value></property>
    </bean>

其中QOS参数timeToLive表示发送的消息在broker保存的最长时间；delievryMode是发送模式（persistent或nonpersistent），要设置QOS参数必须得启用explicitQosEnabled；
调用jmsTemplate的receive时默认是阻塞，可以设置receiveTimeout表示接收数据的超时时间。
如果采用消息监听接收消息，需要配置MessageListener。首先是实现自己的消息监听类，再配置jmsContainer

    <!-- this is the Message Driven POJO (MDP) -->
    <bean id="messageListener" class="com.swiee.controller.ElecMessageListener" />
    <!-- and this is the message listener container -->
    <bean id="jmsContainer" class="org.springframework.jms.listener.DefaultMessageListenerContainer">
    <property name="connectionFactory">
    <ref local="jmsFactory"/>
    </property>
    <property name="destination" ref="consumerDest"/>
    <property name="messageListener" ref="messageListener" />
    <property name="sessionTransacted" value="true"/>
    </bean>

## 2. Spring整合CXF
这个比我想象中简单很多。
在spring的配置文件，或者单独的xml文件中，beans的命名空间加上

    xmlns:jaxws="http://cxf.apache.org/jaxws"
    xsi:schemaLocation="
    http://cxf.apache.org/jaxws
    http://cxf.apache.org/schemas/jaxws.xsd"

然后就可以在配置文件中添加自己的ws相关的bean：

    <bean id="hello" class="com.swiee.webservice.impl.HelloWorldImpl" />
    <jaxws:endpoint id="helloWorld" implementor="#hello" address="/HelloWorld" />

其中hello这个bean就是具体的web service实现;jaxws：endpoint中，id是bean的名字，implementor是具体的实现，address就是web service的访问地址。
## 3. web.xml文件到配置
如果通过web容器发布服务，在web.xml文件中的配置包括spring和cxf。
spring设定监听器或servlet（兼容早期版本风格）启动spring应用上下文：

    <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

CXF使用servlet暴露web service：

    <servlet>
    <description>Apache CXF Endpoint</description>
    <servlet-name>cxf</servlet-name>
    <servlet-class>org.apache.cxf.transport.servlet.CXFServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
    <servlet-name>cxf</servlet-name>
    <url-pattern>/*</url-pattern>
    </servlet-mapping>