---
title: Spring源码学习之 编译源码
date: 2016-09-13 09:19:00
updated: 2017-02-19 09:26:22
tags: 
- golang
- reflect
categories: 
- go

---
## 到 github 下载一份源代码

[https://github.com/spring-projects/spring-framework](https://github.com/spring-projects/spring-framework)

我这里放在 D:\gitclone\spring-framework 目录

构建项目前需要下载gradle,到[http://gradle.org/](http://gradle.org/)下载，然后配置`GRADLE_HOME`和`path`路径

进入spring目录, 执行命令: `gradle eclipse -x :eclipse`


<!--more-->


第一次执行会花比较长的时间,gradle会去下载许多依赖库, 

```bash
D:\gitclone\spring-framework>gradle eclipse -x :eclipse
```
这个过程会比较漫长，有效子项目也可能因为网络问题而中断

也可以在子项目里单独构建的命令：

可以进入到子项目路径下 执行 `gradle cleanidea eclipse`，来单独构建项目

比如`D:\gitclone\spring-framework\spring-websocket>gradle cleanidea eclipse`

## 构建过程碰到的问题

1、spring-core项目里丢失了两个jar 包

spring-objenesis-repack-2.4.jar 和spring-cglib-repack-3.2.3.jar

在`bulid.gradle` 文件里在找到了两个 task  `cglibRepackJar`和`objenesisRepackJar`

http://www.blogjava.net/wldandan/archive/2012/06/27/381605.html
```bash
C:\Users\Administrator>D:

D:\>cd D:\gitclone\spring-framework

D:\gitclone\spring-framework>gradle cglibRepackJar
:buildSrc:compileJava UP-TO-DATE
:buildSrc:compileGroovy UP-TO-DATE
:buildSrc:processResources UP-TO-DATE
:buildSrc:classes UP-TO-DATE
:buildSrc:jar UP-TO-DATE
:buildSrc:assemble UP-TO-DATE
:buildSrc:compileTestJava UP-TO-DATE
:buildSrc:compileTestGroovy UP-TO-DATE
:buildSrc:processTestResources UP-TO-DATE
:buildSrc:testClasses UP-TO-DATE
:buildSrc:test UP-TO-DATE
:buildSrc:check UP-TO-DATE
:buildSrc:build UP-TO-DATE
:spring-core:cglibRepackJar
Download https://repo.spring.io/libs-release/com/googlecode/jarjar/jarjar/1.3/ja
rjar-1.3.pom
Download https://repo.spring.io/libs-release/com/googlecode/jarjar/jarjar/1.3/ja
rjar-1.3.jar
Download https://repo.spring.io/libs-release/cglib/cglib/3.2.3/cglib-3.2.3.pom
Download https://repo.spring.io/libs-release/cglib/cglib-parent/3.2.3/cglib-pare
nt-3.2.3.pom
Download https://repo.spring.io/libs-release/cglib/cglib/3.2.3/cglib-3.2.3.jar

BUILD SUCCESSFUL

Total time: 52.3 secs

D:\gitclone\spring-framework>gradle objenesisRepackJar
:buildSrc:compileJava UP-TO-DATE
:buildSrc:compileGroovy UP-TO-DATE
:buildSrc:processResources UP-TO-DATE
:buildSrc:classes UP-TO-DATE
:buildSrc:jar UP-TO-DATE
:buildSrc:assemble UP-TO-DATE
:buildSrc:compileTestJava UP-TO-DATE
:buildSrc:compileTestGroovy UP-TO-DATE
:buildSrc:processTestResources UP-TO-DATE
:buildSrc:testClasses UP-TO-DATE
:buildSrc:test UP-TO-DATE
:buildSrc:check UP-TO-DATE
:buildSrc:build UP-TO-DATE
:spring-core:objenesisRepackJar
Download https://repo.spring.io/libs-release/org/objenesis/objenesis/2.4/objenes
is-2.4.pom
Download https://repo.spring.io/libs-release/org/objenesis/objenesis-parent/2.4/
objenesis-parent-2.4.pom
Download https://repo.spring.io/libs-release/org/objenesis/objenesis/2.4/objenes
is-2.4.jar

BUILD SUCCESSFUL

Total time: 28.738 secs

D:\gitclone\spring-framework>
```
构建后就行了

2、我发现`spring-oxm` 项目也丢失了`jaxb和xmlbeans jar` 包，

我在 `spring-oxm` 子项目里执行
```bash
D:\gitclone\spring-framework\spring-oxm>gradle compileTestJava 
```
构建后就可以

4、子项目`spring-beans-groovy`提示`GroovyDynamicElementReader`这个类不存在

原因是需要安装一个eclipse的groovy插件。

在eclipse的 Help -> Install New Software 中，添加groovy的下载连接：`http://dist.springsource.org/release/GRECLIPSE/e4.3/`

然后全选后，开始下载，下载完后，重启eclipse，然后clean一下项目就搞定了。

解决了所有错误后，得到我们想要的源码项目,如下图，代码整整齐齐，没有报错很清爽

在官方网站里获取到 入门实例

http://projects.spring.io/spring-framework/
```java
package hello;

public interface MessageService {
    String getMessage();
}
package hello;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class MessagePrinter {

    final private MessageService service;

    @Autowired
    public MessagePrinter(MessageService service) {
        this.service = service;
    }

    public void printMessage() {
        System.out.println(this.service.getMessage());
    }
}
package hello;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.*;

@Configuration
@ComponentScan
public class Application {

    @Bean
    MessageService mockMessageService() {
        return new MessageService() {
            public String getMessage() {
              return "Hello World!";
            }
        };
    }

  public static void main(String[] args) {
      ApplicationContext context = 
          new AnnotationConfigApplicationContext(Application.class);
      MessagePrinter printer = context.getBean(MessagePrinter.class);
      printer.printMessage();
  }
}
```
运行后亲测无误。接下来就可以开始spring 的源码阅读旅程了。

文章来源：[https://my.oschina.net/zhongwenhao/blog/701071](https://my.oschina.net/zhongwenhao/blog/701071)