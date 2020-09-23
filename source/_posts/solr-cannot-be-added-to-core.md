---
title: Error CREATEing SolrCore 'new_core'
date: 2018-09-05 06:25:00
updated: 2018-09-05 16:33:30
tags: 
- android
categories: 
- android

---
![solr_error_createing_solrcore.png][1]
提示缺少配置文件：Error CREATEing SolrCore 'new_core1': Unable to create core [new_core1] Caused by: Can't find resource 'conf/solrconfig.xml' in classpath or 'C:\tomcat8\solr_home\solrhome\new_core1'


<!--more-->


```bash
2018-09-05 16:26:27.396 ERROR (http-nio-8080-exec-1) [   x:new_core1] o.a.s.h.RequestHandlerBase org.apache.solr.common.SolrException: Error CREATEing SolrCore 'new_core1': Unable to create core [new_core1] Caused by: Can't find resource 'conf/solrconfig.xml' in classpath or 'C:\tomcat8\solr_home\solrhome\new_core1'
        at org.apache.solr.core.CoreContainer.create(CoreContainer.java:1084)
        at org.apache.solr.handler.admin.CoreAdminOperation.lambda$static$0(CoreAdminOperation.java:94)
        at org.apache.solr.handler.admin.CoreAdminOperation.execute(CoreAdminOperation.java:380)
        at org.apache.solr.handler.admin.CoreAdminHandler$CallInfo.call(CoreAdminHandler.java:395)
        at org.apache.solr.handler.admin.CoreAdminHandler.handleRequestBody(CoreAdminHandler.java:180)
        at org.apache.solr.handler.RequestHandlerBase.handleRequest(RequestHandlerBase.java:199)
        at org.apache.solr.servlet.HttpSolrCall.handleAdmin(HttpSolrCall.java:734)
        at org.apache.solr.servlet.HttpSolrCall.handleAdminRequest(HttpSolrCall.java:715)
        at org.apache.solr.servlet.HttpSolrCall.call(HttpSolrCall.java:496)
        at org.apache.solr.servlet.SolrDispatchFilter.doFilter(SolrDispatchFilter.java:377)
        at org.apache.solr.servlet.SolrDispatchFilter.doFilter(SolrDispatchFilter.java:323)
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
        at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:198)
        at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96)
        at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:496)
        at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:140)
        at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:81)
        at org.apache.catalina.valves.AbstractAccessLogValve.invoke(AbstractAccessLogValve.java:650)
        at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:87)
        at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:342)
        at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:803)
        at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
        at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:790)
        at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1468)
        at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
        at java.lang.Thread.run(Thread.java:748)
Caused by: org.apache.solr.common.SolrException: Unable to create core [new_core1]
        at org.apache.solr.core.CoreContainer.createFromDescriptor(CoreContainer.java:1156)
        at org.apache.solr.core.CoreContainer.create(CoreContainer.java:1045)
        ... 29 more
Caused by: org.apache.solr.common.SolrException: Could not load conf for core new_core1: Error loading solr config from C:\tomcat8\solr_home\solrhome\new_core1\conf\conf\solrconfig.xml
        at org.apache.solr.core.ConfigSetService.getConfig(ConfigSetService.java:97)
        at org.apache.solr.core.CoreContainer.getConfigSet(CoreContainer.java:1184)
        at org.apache.solr.core.CoreContainer.createFromDescriptor(CoreContainer.java:1131)
        ... 30 more
Caused by: org.apache.solr.common.SolrException: Error loading solr config from C:\tomcat8\solr_home\solrhome\new_core1\conf\conf\solrconfig.xml
        at org.apache.solr.core.SolrConfig.readFromResourceLoader(SolrConfig.java:190)
        at org.apache.solr.core.ConfigSetService.createSolrConfig(ConfigSetService.java:109)
        at org.apache.solr.core.ConfigSetService.getConfig(ConfigSetService.java:91)
        ... 32 more
Caused by: org.apache.solr.core.SolrResourceNotFoundException: Can't find resource 'conf/solrconfig.xml' in classpath or 'C:\tomcat8\solr_home\solrhome\new_core1'
        at org.apache.solr.core.SolrResourceLoader.openResource(SolrResourceLoader.java:407)
        at org.apache.solr.core.SolrResourceLoader.openConfig(SolrResourceLoader.java:362)
        at org.apache.solr.core.Config.<init>(Config.java:120)
        at org.apache.solr.core.Config.<init>(Config.java:90)
        at org.apache.solr.core.SolrConfig.<init>(SolrConfig.java:205)
        at org.apache.solr.core.SolrConfig.readFromResourceLoader(SolrConfig.java:182)
        ... 34 more

2018-09-05 16:26:27.397 INFO  (http-nio-8080-exec-1) [   x:new_core1] o.a.s.s.HttpSolrCall [admin] webapp=null path=/admin/cores params={schema=schema.xml&dataDir=data&name=new_core1&action=CREATE&config=conf/solrconfig.xml&instanceDir=new_core1&wt=json&_=1536135715389} status=400 QTime=38
```
查看文件solrconfig.xml的编码格式是否为utf-8，不是转换
把 `C:\tomcat8\solr_home\solrhome\configsets\_default\conf` copy 一份 到 `C:\tomcat8\solr_home\solrhome\new_core1\conf`
![solr_01.png][2]

然后成功。

如图：
![solr_02.png][3]

  [1]: https://imgs.gnux.cn/usr/uploads/2018/09/845157354.png
  [2]: https://imgs.gnux.cn/usr/uploads/2018/09/2658339697.png
  [3]: https://imgs.gnux.cn/usr/uploads/2018/09/1967916340.png