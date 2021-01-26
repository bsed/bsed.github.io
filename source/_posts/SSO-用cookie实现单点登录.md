---
title: "SSO 用cookie实现单点登录"
categories: [ "Java" ]
tags: [ "java","cookie","sso" ]
draft: false
slug: "sso-implementation-of-single-point-login-with-cookie"
date: "2011-01-20 18:15:00"
---

> 内容来源于网络，并结合自己的整理

## Cookie  使用说明

用到的类 javax.servlet.http.Cookie

构造cookie 的方法 :  new Cookie(键,值)
构造函数如下：
Cookie(java.lang.String name, java.lang.String value)

把cookie发送给客户端
HttpServletResponse.addCookie(javax.servlet.http.Cookie)

取得客户浏览器的cookie,返回的是数组
HttpServletRequest.getCookies()

getComment/setComment  获取/设置Cookie的注释。


<!--more-->


getDomain/setDomain
　　获取/设置Cookie适用的域。一般地，Cookie只返回给与发送它的服务器名字完全相同的服务器。使用这里的方法可以指示浏览器把Cookie返回给同一域内的其他服务器。注意域必须以点开始（例如.sitename.com），非国家类的域（如.com，.edu，.gov）必须包含两个点，国家类的域（如.com.cn，.edu.uk）必须包含三个点。

getMaxAge/setMaxAge
　　获取/设置Cookie过期之前的时间，以秒计。如果不设置该值，则Cookie只在当前会话内有效，即在用户关闭浏览器之前有效，而且这些Cookie不会保存到磁盘上。

若生存时间为负值，代表浏览器关闭Cookie即消失。生存时间为0，代表删除Cookie，生存时间为正数，代表Cookie存在多少秒。

 

getName/setName
　　获取/设置Cookie的名字。本质上，名字和值是我们始终关心的两个部分。由于HttpServletRequest的getCookies方法返回的是一个Cookie对象的数组，因此通常要用循环来访问这个数组查找特定名字，然后用getValue检查它的值。


getPath/setPath
　　获取/设置Cookie适用的路径。如果不指定路径，Cookie将返回给当前页面所在目录及其子目录下的所有页面。这里的方法可以用来设定一些更一般的条件。例如，someCookie.setPath("/")，此时服务器上的所有页面都可以接收到该Cookie。

getSecure/setSecure
　　获取/设置一个boolean值，该值表示是否Cookie只能通过加密的连接（即SSL）发送。

getValue/setValue
　　获取/设置Cookie的值。如前所述，名字和值实际上是我们始终关心的两个方面。不过也有一些例外情况，比如把名字作为逻辑标记（也就是说，如果名字存在，则表示true）。

getVersion/setVersion
　　获取/设置Cookie所遵从的协议版本。默认版本0（遵从原先的Netscape规范）；版本1遵从RFC 2109 ， 但尚未得到广泛的支持。

几个Cookie工具函数,获取指定名字的Cookie值
　　 该函数是ServletUtilities.java的一部分。getCookiue通过循环依次访问Cookie对象数组的各个元素，寻找是否有指定名字的Cookie，如找到，则返回该Cookie的值；否则，返回参数中给出的默认值。getCookiue能够在一定程度上简化Cookie值的提取。

---------------


cookie保存在哪里
在Windows 9X系统计算机中，Cookie文件的存放位置为C：\Windows\Cookies，在Windows NT／2000／ XP的计算机中，Cookie文件的存放位置为C：\Documents and Settings\用户名\Cookie文件夹。

----------------

 

 

下面是代码,是直接从项目中实际代码
*java:*

    public static void setReffer(final HttpServletResponse response, final String userName, final String password)   
    {   
        final String sSession = password + userName;  //密码是SHA1加密,长度为40个字符(160位)   
        Cookie oItem;   
        // 因为Cookie 中不允许保存特殊字符, 所以采用 BASE64 编码，CookieUtil.encode()是BASE64编码方法,略..   
        oItem = new Cookie("SSO", CookieUtil.encode(sSession));   
        oItem.setDomain(.google.com);  //请用自己的域   
        oItem.setMaxAge(-1); //关闭浏览器后，cookie立即失效           
        oItem.setPath("/");   
        response.addCookie(oItem);   
    }  


*java:* 

    public static String[] checkReffer(final HttpServletRequest request)   
    {   
        final Cookie[] oCookies = request.getCookies();   
        if (oCookies != null)   
        {   
            for (final Cookie oItem : oCookies)   
            {   
                final String sName = oItem.getName();   
      
                if (sName.equals("SSO"))   
                {   
                    final String sSession = CookieUtil.decode(oItem.getValue()); //解码 CookieUtil.decode()是BASE64解码方法,略..   
                    if (sSession.length() > 40)   
                    {   
                        // 获得存储在 Cookies 中的用户名称和密码   
                        final String sUser = sSession.substring(40);   
                        final String sPass = sSession.substring(0, 40);   
                        final String[] strArr =   
                        { sUser, sPass };   
                        return strArr; //返回用户名,密码   
                    }   
                }   
            }   
        }   
        return null;   
    }  

