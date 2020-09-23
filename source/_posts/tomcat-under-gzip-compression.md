---
title: Tomcat下开启Gzip压缩
date: 2013-10-02 09:06:00
updated: 2015-10-01 09:08:40
tags: 
- jquery
- validate
categories: 
- js

---
HTTP 压缩可以大大提高浏览网站的速度，它的原理是，在客户端请求服务器对应资源后，从服务器端将资源文件压缩，再输出到客户端，由客户端的浏览器负责解压缩并浏览。相对于普通的浏览过程HTML ,CSS,Javascript , Text ，它可以节省40%左右的流量。更为重要的是，它可以对动态生成的，包括CGI、PHP , JSP , ASP , Servlet,SHTML等输出的网页也能进行压缩，压缩效率也很高

使用方法：在tomcat的server.xml文件中：

    <Connector port="8080" protocol="HTTP/1.1"
                   connectionTimeout="20000"
                   redirectPort="8443"
                   compression="on"  
                   compressionMinSize="2048"  
                   noCompressionUserAgents="gozilla, traviata"  
                   compressableMimeType="text/html,text/xml,text/javascript,text/css,text/plain"
                   />

其中：
compression="on"   
  compressionMinSize="2048"   
  noCompressionUserAgents="gozilla, traviata"   
  compressableMimeType="text/html,text/xml,text/javascript,text/css,text/plain" 
为添加的内容：


<!--more-->


compression="on"  为启用tomcat的gzip压缩

 compressionMinSize="2048"   为当文件大小不小于2kb时候开始压缩

 noCompressionUserAgents="gozilla, traviata"   为不支持压缩的浏览器

  compressableMimeType="text/html,text/xml,text/javascript,text/css,text/plain" 为支持压缩的文件格式

*java测试类：*

    package com.best.oa.workshop.gzip;
     
    import org.apache.commons.httpclient.HttpClient;
    import org.apache.commons.httpclient.methods.GetMethod;
    /**
     * Created with IntelliJ IDEA.
     * User: test
     * Date: 13-9-24
     * Time: 下午4:14
     * To change this template use File | Settings | File Templates.
     */
    public class Gzip {
        public static void main(String[] args) throws Exception{
            HttpClient http = new HttpClient();
            GetMethod get = new GetMethod("http://localhost:8080/login.htm");
            try{
                get.addRequestHeader("accept-encoding", "gzip,deflate");
                get.addRequestHeader("user-agent", "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.0; Alexa Toolbar; Maxthon 2.0)");
                int er = http.executeMethod(get);
                if(er==200){
                    System.out.println(get.getResponseContentLength());
                    String html = get.getResponseBodyAsString();
                    System.out.println(html);
                    System.out.println(html.getBytes().length);
                }
            }finally{
                get.releaseConnection();
            }
        }
    }

当控制台看到输出为乱码，并且文件大小比较配置前小了，则证明成功了。
检测自己的网站是否开启Gzip开启压缩功能呢？

http://seo.chinaz.com

网址中输入自己的网站域名：

