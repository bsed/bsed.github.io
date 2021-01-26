---
title: "CXF添加自定义拦截器"
categories: [ "Java" ]
tags: [ "java","cfx" ]
draft: false
slug: "add-custom-interceptor-cxf"
date: "2015-02-03 21:08:00"
---

今天我们来讲下如何添加自定义拦截器；

我们的实例是客户端访问服务端webservice接口要加权限认证。

我们思路先说下。我们可以通过在SOAP消息的Header头信息中添加自定义信息，然后发送到服务端端，服务器端通过获取

Header头消息，然后进行认证；这里的添加消息，和获取消息认证，我们都是通过自定义拦截器来实现；

OK下面我们来实现下：

首先是服务器端：

我们自定义拦截器：`MyInterceptor`


<!--more-->


```java
package com.mbioq.ws.interceptor;
 
import java.util.List;
 
import org.apache.cxf.binding.soap.SoapMessage;
import org.apache.cxf.headers.Header;
import org.apache.cxf.interceptor.Fault;
import org.apache.cxf.phase.AbstractPhaseInterceptor;
import org.apache.cxf.phase.Phase;
import org.w3c.dom.Element;
import org.w3c.dom.NodeList;
 
/**
 * 自定义拦截器
 * @author Administrator
 *
 */
public class MyInterceptor extends AbstractPhaseInterceptor<SoapMessage>{
 
    public MyInterceptor(){
        // 在调用方法之前调用拦截器
        super(Phase.PRE_INVOKE);
    }
 
    /**
     * 拦截获取消息
     */
    public void handleMessage(SoapMessage message) throws Fault {
        List<Header> headers=message.getHeaders();
        if(headers==null || headers.size()==0){
            throw new Fault(new IllegalArgumentException("没有Header,拦截器实施拦截"));
        }
        Header firstHeader=headers.get(0);
        Element ele=(Element) firstHeader.getObject();
        NodeList userIds=ele.getElementsByTagName("userName");
        NodeList userPasses=ele.getElementsByTagName("password");
         
        if(userIds.getLength()!=1){
            throw new Fault(new IllegalArgumentException("用户名格式不对"));
        }
         
        if(userPasses.getLength()!=1){
            throw new Fault(new IllegalArgumentException("密码格式不对"));
        }
         
        String userId=userIds.item(0).getTextContent();
        String userPass=userPasses.item(0).getTextContent();
         
        if(!userId.equals("kelvin") || ! userPass.equals("123456")){
            throw new Fault(new IllegalArgumentException("用户名或者密码不正确"));
        }
         
    }
}
```

这里的话，我们主要是获取Header头消息，然后获取userName和password节点，然后获取值，进行权限判断，假如认证不通过，我们抛出异常；

在Server类里，我们要添加一个in 拦截器，在进入的时候，我们要进行验证；

```java
package com.mbioq.ws;
 
import org.apache.cxf.interceptor.LoggingInInterceptor;
import org.apache.cxf.interceptor.LoggingOutInterceptor;
import org.apache.cxf.jaxws.JaxWsServerFactoryBean;
 
import com.java1234.interceptor.MyInterceptor;
import com.java1234.webservice.impl.HelloWorldImpl;
 
public class Server {
 
    public static void main(String[] args) {
        System.out.println("web service start");  
        HelloWorld helloWorldImpl = new HelloWorldImpl();  
        String address = "http://192.168.1.103:7766/helloWorld";
        JaxWsServerFactoryBean factoryBean = new JaxWsServerFactoryBean();
        factoryBean.setAddress(address); // 设置暴露地址
        factoryBean.setServiceClass(HelloWorld.class); // 接口类
        factoryBean.setServiceBean(helloWorldImpl); // 设置实现类
        factoryBean.getInInterceptors().add(new LoggingInInterceptor()); // 添加in拦截器 日志拦截器
        factoryBean.getOutInterceptors().add(new LoggingOutInterceptor()); // 添加out拦截器
         
        factoryBean.getInInterceptors().add(new MyInterceptor()); // 添加自定义拦截器
        factoryBean.create();      
        System.out.println("web service started");  
    }
}
```

接下来是修改客户端代码：

我们同样要添加一个自定义拦截器：`AddHeaderInterceptor`

```java
package com.mbioq.ws.interceptor;
 
 
import java.util.List;
 
import javax.xml.namespace.QName;
 
import org.apache.cxf.binding.soap.SoapMessage;
import org.apache.cxf.headers.Header;
import org.apache.cxf.helpers.DOMUtils;
import org.apache.cxf.interceptor.Fault;
import org.apache.cxf.phase.AbstractPhaseInterceptor;
import org.apache.cxf.phase.Phase;
import org.w3c.dom.Document;
import org.w3c.dom.Element;
 
public class AddHeaderInterceptor extends AbstractPhaseInterceptor<SoapMessage> {
 
    private String userName;
    private String password;
 
    public AddHeaderInterceptor(String userName, String password) {
        super(Phase.PREPARE_SEND); // 发送SOAP消息之前调用拦截器
        this.userName=userName;
        this.password=password;
    }
 
    public void handleMessage(SoapMessage message) throws Fault {
        List<Header> headers=message.getHeaders();
         
        Document doc=DOMUtils.createDocument();
        Element ele=doc.createElement("authHeader");
        Element idElement=doc.createElement("userName");
        idElement.setTextContent(userName);
        Element passElement=doc.createElement("password");
        passElement.setTextContent(password);
         
        elem.appendChild(idElement);
        elem.appendChild(passElement);
         
        headers.add(new Header(new QName("kelvin"),elem));
         
         
    }
 
     
}
```

这里的话，我们主要是在拦截器里创建头消息；



Client类里我们要修改下，加下Out 拦截器：

```java
package com.mbioq.ws.webservice;
 
import java.util.List;
 
import org.apache.cxf.frontend.ClientProxy;
import org.apache.cxf.interceptor.LoggingInInterceptor;
import org.apache.cxf.interceptor.LoggingOutInterceptor;
 
import com.java1234.interceptor.AddHeaderInterceptor;
 
public class Client {
 
    public static void main(String[] args) {
        HelloWorldService service=new HelloWorldService();
        HelloWorld helloWorld=service.getHelloWorldPort();
        org.apache.cxf.endpoint.Client client=ClientProxy.getClient(helloWorld);
        // client.getInInterceptors().add(new LoggingInInterceptor()); // 添加in拦截器 日志拦截器
        client.getOutInterceptors().add(new AddHeaderInterceptor("java1234","123456")); // 添加自定义拦截器
        client.getOutInterceptors().add(new LoggingOutInterceptor()); // 添加out拦截器
        //System.out.println(helloWorld.say("java1234"));
        /*User user=new User();
        user.setUserName("jack");
        user.setPassword("123456");
        List<Role> roleList=helloWorld.getRoleByUser(user);
        for(Role role:roleList){
            System.out.println(role.getId()+","+role.getRoleName());
        }*/
        MyRoleArray array=helloWorld.getRoles();
        List<MyRole> roleList=array.item;
        for(int i=0;i<roleList.size();i++){
            MyRole my=roleList.get(i);
            System.out.print(my.key+":");
            for(Role r:my.value){
                System.out.print(r.getId()+","+r.getRoleName()+" ");
            }
            System.out.println();
        }
    }
}
```

OK这样就完整了自定义拦截器实现权限认证；



先运行Server类，和以前一样；



假如我们把  client.getOutInterceptors().add(new AddHeaderInterceptor("kelvin","123456")); // 添加自定义拦截器

密码改成 123 


然后运行Client类，会报错；


用户名或者密码不正确；

