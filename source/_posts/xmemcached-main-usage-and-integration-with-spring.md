---
title: Xmemcached主要用法及与Spring集成
date: 2015-10-19 21:27:00
updated: 2015-10-19 21:29:29
tags: 
- js
categories: 
- js

---
xMemcached是memcached的一个java客户端，基于java nio，支持memcached的所有协议。本文简要介绍xMemcached的基本用法，以及与Spring的集成配置。

## xMemcached的主要方法示例

	/**
	 * XMemcachedClientBuilder是MemcachedClientBuilder的一个实现类，XMemcachedClient是MemcachedClient的一个实现类；
	 * 在实际使用中，应该使用接口，利用多态特性，这里直接使用实现类，是为了方便查看实现类的源码。
	 */
	/**
	 * MemcachedClientBuilder builder = new XMemcachedClientBuilder(AddrUtil.getAddresses("192.168.56.200:11211"));
	 * MemcachedClient memcachedClient = null;
	 */


<!--more-->


	XMemcachedClientBuilder builder = new XMemcachedClientBuilder(AddrUtil.getAddresses("192.168.56.200:11211 192.168.56.200:11212"));
	XMemcachedClient xMemcachedClient = null;

	// 注意处理相关异常

	// 默认是采用余数哈希，可以修改为一致性哈希
	builder.setSessionLocator(new KetamaMemcachedSessionLocator());
	// 启用二进制协议，getAndTouch等方法仅在二进制协议中支持
	builder.setCommandFactory(new BinaryCommandFactory());

	// build the memcached client
	xMemcachedClient = (XMemcachedClient) builder.build();

	// set: 第一个参数是key，第二个参数是超时时间，第三个参数是value
	xMemcachedClient.set("first", 0, "tianjin");
	xMemcachedClient.add("second", 20, "chengdu");
	xMemcachedClient.replace("first", 0, "Beijing");

	// get：根据key获取value，第一个参数为key，第二个为超时；
	String firstValue = xMemcachedClient.get("first", new StringTranscoder());
	logger.info("get first value: {}", xMemcachedClient.get("first"));

	// delete: 删除item
	xMemcachedClient.delete("second");
	xMemcachedClient.set("third", 20, "xian");
	xMemcachedClient.deleteWithNoReply("third");

	// touch：修改item过期时间
	xMemcachedClient.touch("first", 0);
	firstValue = xMemcachedClient.getAndTouch("first", 10);
	logger.info("getAndTouch op, first value: {}", firstValue);

	// append, prepend: 追加数据
	xMemcachedClient.append("first", ", come on");
	xMemcachedClient.prepend("first", "hello ");
	logger.info("append and prepend op, first value: {}", xMemcachedClient.get("first"));

	/**
	 * cas: 通过gets操作返回GetResponse，其中包括value值和cas值
	 */
	GetsResponse<String> response = xMemcachedClient.gets("first");
	long cas = response.getCas();
	if (!xMemcachedClient.cas("first", 0, "guangzhou", cas)) {
		logger.info("cas error");
	}

	// cas: 重试更方便；第一个方法表示重试次数，第二个方法表示更新的值
	xMemcachedClient.cas("first", 0, new CASOperation<String>() {
		@Override
		public int getMaxTries() {
			return 1;
		}

		@Override
		public String getNewValue(long currentCAS, String currentValue) {
			return "xian";
		}
	});
	logger.info("cas op, first value: {}", xMemcachedClient.get("first"));

	// incr/decr: 值的增加减少
	xMemcachedClient.incr("id", 3, 0);
	xMemcachedClient.incr("id", 5);
	xMemcachedClient.decr("id", 2);
	logger.info("incr/decr op, value: {}", xMemcachedClient.get("id"));

	// namespace: set时指定命名空间，get时也需要指定，可以使命名空间中的所有items失效
	String nsSohu = "sohu";
	xMemcachedClient.withNamespace(nsSohu, new MemcachedClientCallable<Object>() {
		@Override
		public Object call(MemcachedClient client) throws MemcachedException, InterruptedException, TimeoutException {
			client.set("typeTv", 0, "tv");
			client.set("typeMv", 0, "mv");
			client.set("typeDrama", 0, "drama");
			return null;
		}
	});

	logger.info("namespace op, typeTv: {}", xMemcachedClient.withNamespace(nsSohu, new MemcachedClientCallable<String>() {
		@Override
		public String call(MemcachedClient client) throws MemcachedException, InterruptedException, TimeoutException {
			return client.get("typeTv");
		}
	}));
	xMemcachedClient.invalidateNamespace(nsSohu);

	// stats: 获取统计，也可以根据items获取统计
	Map<InetSocketAddress, Map<String, String>> stats = xMemcachedClient.getStats();
	for (InetSocketAddress addr: stats.keySet()) {
		logger.info("stats map: {}: {}", addr, stats.get(addr).toString());
	}

	// flush_all: 是所有item都过期
	xMemcachedClient.flushAll("192.168.56.200:11211");

	// 删除一个server
	xMemcachedClient.removeServer("192.168.56.200:11212");

## xMemcached与Spring集成
	
> applicationContext.xml的配置：

    <!--定义一个server-->
    <bean name="server1" class="java.net.InetSocketAddress">
        <constructor-arg><value>192.168.56.200</value></constructor-arg>
        <constructor-arg><value>11211</value></constructor-arg>
    </bean>

    <!--定义XMemcachedClientBuilder实例，第一个参数设置server，第二个参数设置权重-->
    <bean id="memcachedClientBuilder" class="net.rubyeye.xmemcached.XMemcachedClientBuilder">
        <constructor-arg>
            <list>
                <bean class="java.net.InetSocketAddress">
                    <constructor-arg><value>192.168.56.200</value></constructor-arg>
                    <constructor-arg><value>11211</value></constructor-arg>
                </bean>
                <bean class="java.net.InetSocketAddress">
                    <constructor-arg><value>192.168.56.200</value></constructor-arg>
                    <constructor-arg><value>11212</value></constructor-arg>
                </bean>
            </list>
        </constructor-arg>
        <constructor-arg>
            <list>
                <value>1</value>
                <value>2</value>
            </list>
        </constructor-arg>

        <!--&lt;!&ndash;设置验证信息&ndash;&gt;-->
        <!--<property name="authInfoMap">-->
            <!--<map>-->
                <!--<entry key-ref="server1">-->
                    <!--<bean class="net.rubyeye.xmemcached.auth.AuthInfo" factory-method="typical">-->
                        <!--<constructor-arg index="0"><value>index</value></constructor-arg>-->
                        <!--<constructor-arg index="1"><value>index-pd</value></constructor-arg>-->
                    <!--</bean>-->
                <!--</entry>-->
            <!--</map>-->
        <!--</property>-->

        <!--设置线程池-->
        <property name="connectionPoolSize" value="2"></property>

        <!--使用二进制协议-->
        <property name="commandFactory">
            <bean class="net.rubyeye.xmemcached.command.BinaryCommandFactory"></bean>
        </property>

       <!--设置序列化方式-->
        <property name="transcoder">
            <bean class="net.rubyeye.xmemcached.transcoders.SerializingTranscoder"></bean>
        </property>

        <!--设置一致性哈希-->
        <property name="sessionLocator">
            <bean class="net.rubyeye.xmemcached.impl.KetamaMemcachedSessionLocator"></bean>
        </property>

    </bean>

    <!--定义memcachedClient，通过memcachedClientBuilder的build方法-->
    <bean name="memcachedClient" factory-bean="memcachedClientBuilder"
          factory-method="build" destroy-method="shutdown">
	</bean>


> java测试类：
	
	MemcachedClient memcachedClient = null;
	ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
	try {
		memcachedClient = (MemcachedClient) applicationContext.getBean("memcachedClient");
		memcachedClient.set("spring", 0, "3.0.0.RELEASE");
		logger.info("spring: {}", memcachedClient.get("spring"));
		memcachedClient.replace("spring", 0, "4.0.0.RELEASE is coming");
		logger.info("spring, {}", memcachedClient.get("spring"));
	} catch (Exception e) {
		logger.info("spring test", e);
	}


### 参考
+ [xmemcached user_guide_zh](https://code.google.com/p/xmemcached/wiki/User_Guide_zh)
