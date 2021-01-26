---
title: "Spring依赖注入的方式之一：通过XML配置文件实现"
categories: [ "Java" ]
tags: [ "java","DI","xml","spring" ]
draft: false
slug: "one-of-the-ways-of-spring-dependency-injection-the-implementation-of-the-xml-configuration-file"
date: "2013-10-20 21:23:00"
---

> 因为项目中一直要用到Spring相关的知识，所以最近在看[Spring实战](http://www.amazon.cn/Spring%E5%AE%9E%E6%88%98-%E6%B2%83%E5%B0%94%E6%96%AF/dp/B00CY6UD2I/ref=sr_1_1?ie=UTF8&qid=1394286577&sr=8-1&keywords=spring%E5%AE%9E%E6%88%98)这本书，希望对Spring有一个整理的了解和把握。不得不说，这是一本好书，中文译本也不错。这个系列，记录自己关于Spring的笔记和理解，一是加深理解，同时也希望对别人有所帮助。

## 1. 通过构造方法和xml配置注入

> 定义带参数的构造函数，使依赖对象作为构造函数的参数： 

	public class VideoInfoServiceImpl implements VideoInfoService {
		VideoInfoDao videoInfoDao;

		public VideoInfoServiceImpl() {}

		public VideoInfoServiceImpl(VideoInfoDao videoInfoDao) {
			this.videoInfoDao = videoInfoDao;
		}

		@Override
		public List<Map<String, Object>> getVideos(Map<String, Object> queryMap) {
			List<Map<String, Object>> videoList = videoInfoDao.getVideos(queryMap);
			return videoList;
		}
	}


<!--more-->


> 在定义bean时，使用`constructor-arg`标签，name表示构造函数的参数，ref表示引用另一个bean；参数也可以为基本类型，此时使用value标签，而不是ref。(如果不使用`constructor-arg`，则bean实例化时调用的是默认的不带参数的构造函数)

    <bean id="videoInfoServiceImpl" class="com.sohu.tv.service.impl.VideoInfoServiceImpl">
        <constructor-arg name="videoInfoDao" ref="videoInfoDaoImpl"/>
    </bean>

    <bean id="videoInfoServiceImpl" class="com.sohu.tv.service.impl.VideoInfoServiceImpl">
        <constructor-arg name="id" value="25"/>
    </bean>

## 2. 通过setter方法和xml配置注入

> 定义依赖对象的setter方法，在bean定义时，使用`property`标签表示调用指定属性的setter方法，实现注入：

	public class VideoInfoServiceImpl implements VideoInfoService {
		VideoInfoDao videoInfoDao;

		@Override
		public List<Map<String, Object>> getVideos(Map<String, Object> queryMap) {
			List<Map<String, Object>> videoList = videoInfoDao.getVideos(queryMap);
			return videoList;
		}

		public void setVideoInfoDao(VideoInfoDao videoInfoDao) {
			this.videoInfoDao = videoInfoDao;
		}
	}

> property指定属性为videoInfoDao，则bean实例化后调用setVideoInfoDao方法实现注入；ref表示引用另一个bean，value表示基本类型值，int, float, true等。

    <bean id="videoInfoServiceImpl" class="com.sohu.tv.service.impl.VideoInfoServiceImpl">
        <property name="videoInfoDao" ref="videoInfoDaoImpl"/>
    </bean>

> 此时也可以通过`bean`的p属性实现注入，比较简洁，需要加入p命令空间，和`property`方式是等价的。
	
	xmlns:p="http://www.springframework.org/schema/p"

    <bean id="videoInfoServiceImpl" class="com.sohu.tv.service.impl.VideoInfoServiceImpl"
            p:videoInfoDao-ref="videoInfoDaoImpl" />

## 3. 通过SpEL表达式注入

> SpEL可以实现在运行时装配。SpEL通过#{}获取变量的值，{}中既可以是值类型，也可以是引用类型，通过bean的id引用其它的bean，此时，在SpEL中通过value引用其它的bean和通过ref直接引用bean的效果是相同的。

	<bean id="videoInfoServiceImpl" class="com.sohu.tv.service.impl.VideoInfoServiceImpl">
        <!--<property name="videoInfoDao" ref="videoInfoDaoImpl"/>-->
        <property name="videoInfoDao" value="#{videoInfoDaoImpl}" />
    </bean>

## 4. 通过setter方法和autowire="byName"自动装配注入

> 根据bean的名字进行注入，使用autowire="byName"，则寻找与属性名字相同的bean，通过属性的setter方法注入。

    <bean id="saveVideoInfoDao" class="org.dao.SaveVideoInfoDao"/>
    <bean id="videoService" class="org.service.VideoService" autowire="byName"/>

## 5. 通过setter方法和autowire="byType"自动装配注入

> 根据bean的类型进行注入，使用autowire="byType"，则寻找与属性名字相同的bean，通过属性的setter方法注入：

    <bean id="saveVideoInfoDao" class="org.dao.SaveVideoInfoDao"/>
    <bean id="videoService" class="org.service.VideoService" autowire="byType"/>

## 6. 通过factory-method给单例类定义bean

> 如果类的构造函数不可用，比如单例类，构造函数私有，通过public的接口获取实例；定义单例类的bean时，使用`factory-method`，表示调用类的静态方法构造实例：

	<bean id="singleton" class="yousharp.Singleton" factory-method="getInstance"/>
    <bean id="factoryService" class="yousharp.FactoryService">
        <constructor-arg name="singleton" ref="singleton"/>
    </bean>

## 7. 定义bean的作用域

> 默认是singleton，可以设置scope属性为prototype，表示每次调用都创建一个新实例：

    <bean id="videoInfoServiceImpl" class="com.sohu.tv.service.impl.VideoInfoServiceImpl"
		scope="prototype"/>

## 8. 装配list

> 数组或Collection的任意实现如List, ArrayList等都可以使用list和set来装配，区别在于set中不允许有重复元素。定义list成员的setter方法：
	
	private List<VideoInfoDao> videoInfoDaoList;

	public void setVideoInfoDaoList(List<VideoInfoDao> videoInfoDaoList) {
		this.videoInfoDaoList = videoInfoDaoList;
	}

> 在xml配置文件里，在`property`里定义`list`，给list成员注入实例：

    <bean id="videoInfoServiceImpl" class="com.sohu.tv.service.impl.VideoInfoServiceImpl">
        <property name="videoInfoDaoList">
            <list>
                <ref bean="jdbcDaoImple"/>
                <ref bean="mybatisDaoImpl"/>
                <ref bean="hibernateDaoImpl"/>
            </list>
        </property>
    </bean>

## 9. 装配map

> 如果需要对一个map成员变量进行注入，在`property`里，通过`map`来实现注入，其中key和value都可以是值类型或引用类型；

	private Map<String, VideoInfoDaoImpl> videoInfoDaoMap ;

	public void setVideoInfoDaoList(Map<String, VideoInfoDaoImpl> videoInfoDaoMap) {
		this.videoInfoDaoMap = videoInfoDaoMap;
	}

	<bean id="videoInfoServiceImpl" class="com.sohu.tv.service.impl.VideoInfoServiceImpl">
        <property name="videoInfoDaoList">
            <map>
                <entry key="jdbc" value-ref="videoInfoDaoImpl"/>
                <entry key="mybatis" value-ref="videoInfoDaoImpl"/>
                <entry key="hibernate" value-ref="videoInfoDaoImpl"/>
            </map>
        </property>
    </bean>

## 10. 装配property
> property也是key-value对，与map的区别是，其key和value必须都是String类型。

	Properties props;
	public void setProps(Properties props) {
		this.props = props;
	}

    <bean id="videoInfoServiceImpl" class="com.sohu.tv.service.impl.VideoInfoServiceImpl" >
        <property name="props">
            <props>
                <prop key="daily">30 00 * * *</prop>
                <prop key="weekly">30 00 * * 1</prop>
                <prop key="monthly">30 00 * 1 *</prop>
            </props>
        </property>
    </bean>

### 参考资料

+ [Spring实战(第3版)](http://www.amazon.cn/Spring%E5%AE%9E%E6%88%98-%E6%B2%83%E5%B0%94%E6%96%AF/dp/B00CY6UD2I/ref=sr_1_1?ie=UTF8&qid=1394943496&sr=8-1&keywords=spring+in+action)
