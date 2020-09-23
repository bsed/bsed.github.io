---
title: 为什么应该选择gradle
date: 2015-10-20 21:25:00
updated: 2016-01-24 08:29:53
tags: 
- javascript
- function
categories: 
- js

---
![why-should-choose-gradle.jpg][1]
## 1. 为什么选择Gradle？

## 2. 简单入门

### 2.1 使用Intellij Idea创建gradle项目

首先在Idea中启用Gradle支持：Settings->Plugins: Gradle
	
然后创建一个gradle项目或模块，会发现目录结构和maven的很像，其中build.gradle是gradle的配置文件，类似于maven中pom.xml文件，以下是build.gradle的简单示例：
	


<!--more-->


	apply plugin: 'java'

	group = 'org.yousharp'
	version = '1.0-SNAPSHOT'

	sourceCompatibility = 1.7
	targetCompatibility = 1.7

	repositories {
		mavenCentral()
		maven { url "http://repo.maven.apache.org/maven2" }
	}
	dependencies {
		compile group: 'com.google.guava', name: 'guava', version:'17.0'
		compile group: 'redis.clients', name: 'jedis', version:'2.6.0'
		testCompile group: 'junit', name: 'junit', version:'4.11'
	}

插件(plugin)：是gradle的一种扩展，gradle预定义了很多插件，常见的如java插件、war插件等；java插件中定义了一些有用的task，如编译源码、执行单元测试、生成jar包、约定默认的目录结构等；
repositories定义仓库，dependencies定义项目的依赖，比maven的XML定义更简洁；

那么，如何编译、测试，以及运行gradle的项目呢？

刚才说到java插件预定义了很多task，其中就包括编译、测试、生成jar包等task，可以在命令行通过`$ gradle tasks`查看项目已定义的所有task以及含义，如java插件中常用的task有：

	+ assemble: 编译
	+ build：编译并执行测试
	+ clean：删除build目录
	+ jar： 生成jar包
	+ test：执行单元测试
	
### 2.2 将Java项目从maven迁移到gradle

如何将一个java项目从maven迁移到gradle呢？gradle集成了一个很方便的插件：Build Init Plugin，使用这个插件可以很方便地创建一个新的gradle项目，或者将其它类型的项目转换为gradle项目。

要将maven项目转换为gradle项目，只需要在项目的pom文件所在的目录下执行以下命令：

	$ gradle init --type pom
	
上面的命令会根据pom文件自动生成gradle项目所需的文件和配置，然后以gradle项目重新导入即可。

关于gradle的简介就到这里，感兴趣的朋友可以参考[gradle-userguide](http://gradle.org/docs/current/userguide/userguide.html)以及[Gradle in Action](http://www.gradle.org/books)。

### 参考
	
+ [gradle-userguide](http://gradle.org/docs/current/userguide/userguide.html)
+ [Gradle in Action](http://www.gradle.org/books)


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/3922610267.jpg