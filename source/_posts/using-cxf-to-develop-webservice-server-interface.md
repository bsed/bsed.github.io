---
title: 使用CXF开发WebService服务器端接口
date: 2015-12-08 20:12:00
updated: 2017-02-07 21:07:52
tags: 
- nodejs
categories: 
- js

---
CXF作为java领域主流的WebService实现框架，Java程序员有必要掌握它。

CXF主页：http://cxf.apache.org/

首先建一个Maven的j2se项目；
![Selection_001.png][1]
项目的jre用1.8，因为1.7有webservice的默认实现。不要用1.5 不然下面你用我的代码会有问题，用1.5的话，还需要另外加jar包，这里为了大家省事，要换成1.7;

![Selection_002.png][2]
根据规范，我们先建一个接口类：`HelloWorld`
```java
package ws_service;

import javax.jws.WebService;

@WebService
public interface HelloWorld {
	public String say(String str);
}

```

再建一个具体的实现类：`HelloWorldImpl`

```java
package ws_service;

import javax.jws.WebService;

@WebService
public class HelloWorldImpl implements HelloWorld {

	public String say(String str) {
		return "Hello "+str;
	}

}
```


最后建一个发布服务的主类：`Server`

```java
package ws_service;

import javax.xml.ws.Endpoint;

public class Server {
	public static void main(String[] args) {
		System.out.println("web service start");
        HelloWorld implementor = new HelloWorldImpl();

        String address = "http://192.168.1.103:7766/helloWorld";
        Endpoint.publish(address, implementor);  // JDK实现
        System.out.println("web service started");

	}
}

```

这里的Endpoint是Jdk自身实现的WebService。所以到这里我们不需要用到CXF的任何东西。

这里的address，写上自己的本机IP

我们运行下Server类：

运行效果如下：

![Selection_005.png][3]
我们在浏览器里访问：http://192.168.1.103:7766/helloWorld?wsdl

效果：
![Selection_006.png][4]
说明已经成功调用了webservice接口；
这一大串xml标记，其实就是wsdl （Web Services Description Language） 也就是Web Service描述语言，描述服务器端定义的webservice接口的相关信息，

当然我们不用花时间去学习这东西，毕竟这东西是一套规范，但是我们仔细观察，猜测，也能分析出一些猫腻来，这里wsdl:types就是定义了一些变量，wsdl:message相当于定义方法，

wsdl:portType相当与调用与返回，下面的话，就是一些操作。这东西不必要去研究细节，搞这东西会晕死的。有工具给我处理。我们角度要站得高。

客户端通过这个url请求，获得wsdl规范的xml文档片段信息，然后这里又涉及到一个新的概念SOAP（Simple Object Access Protocol）即简单对象访问协议，我们通过这个协议，来实现客户端，服务器端消息交互，SOAP使用XML消息调用远程方法；当然交互的媒介就是xml，具体交互内容根据wsdl文档描述来。

这里的wsdl 是 Web Services Description Language的缩写，是一个用来描述Web服务和说明如何与Web服务通信的XML语言。WSDL是Web Service的描述语言，用于描述Web Service的服务，接口绑定等，为用户提供详细的接口说明书。

请求后得到的是一个xml规范文档。是一套规范，后面会具体介绍，任何语言平台技术都可以解析。

下面我们介绍使用CXF来实现webservice接口：

我们先在pom.xml中加入：
```xml
<dependency>
        <groupId>org.apache.cxf</groupId>
        <artifactId>cxf-core</artifactId>
        <version>3.1.5</version>
    </dependency>
     
    <dependency>
        <groupId>org.apache.cxf</groupId>
        <artifactId>cxf-rt-frontend-jaxws</artifactId>
        <version>3.1.5</version>
    </dependency>
     
    <dependency>
        <groupId>org.apache.cxf</groupId>
        <artifactId>cxf-rt-transports-http-jetty</artifactId>
        <version>3.1.5</version>
    </dependency>
```

这里要额外加入jetty，作为webservice发布的服务器。jetty是一个内嵌的web服务器；
![Selection_007.png][5]
我们把Server改下。换成CXF实现：

```java
package ws_service;

import javax.xml.ws.Endpoint;

import org.apache.cxf.jaxws.JaxWsServerFactoryBean;

public class Server {
	public static void main(String[] args) {
		System.out.println("web service start");
        HelloWorld implementor = new HelloWorldImpl();

        String address = "http://192.168.1.103:7766/helloWorld";
        //Endpoint.publish(address, implementor);  // JDK实现
        JaxWsServerFactoryBean factoryBean = new JaxWsServerFactoryBean();
        factoryBean.setAddress(address); // 设置暴露地址
        factoryBean.setServiceClass(HelloWorld.class); // 接口类
        factoryBean.setServiceBean(implementor); // 设置实现类
        factoryBean.create();
        System.out.println("web service started");

	}
}

```

运行效果和刚才一样，这里就不再重复；


  [1]: https://imgs.gnux.cn/usr/uploads/2017/02/3330572232.png
  [2]: https://imgs.gnux.cn/usr/uploads/2017/02/2756453099.png
  [3]: https://imgs.gnux.cn/usr/uploads/2017/02/1556284863.png
  [4]: https://imgs.gnux.cn/usr/uploads/2017/02/50718041.png
  [5]: https://imgs.gnux.cn/usr/uploads/2017/02/4268193826.png