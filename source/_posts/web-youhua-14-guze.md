---
title: 前端优化的14准则——来自雅虎
date: 2012-01-24 12:44:00
updated: 2014-12-25 12:59:16
tags: 
- css2
- rotate
- transform
categories: 
- css

---
最近在学习JS，然而发现随着JS的增加，网页的响应速度也在随着变慢，现在还是本地的网页，如果这样的东西放在服务器上，会给客户端造成多少压力呢？于是尝试去寻找一些解决方案，下面的内容来自两篇博文。

这是我的站点一个速度诊断图，可以看出网站打开的很大一部分时间是在访问例如js，图片一类的网页元素上，这也就意味着我们可以通过优化去减少不必的加载文件，或者访问次数来达到优化博客的目的。
# 为什么要从前端开始着手？ #

> 1.这里有提升和改进的潜力。如果能减少一半的体积，就能减少40%的响应时间。
> 2.改进前端比改进后端需要的时间和资源更少。（改进后端要重新设计应用程序规划，代码，寻找优化代码的方法，添加或改变硬件配置，分布式数据库，等等）
> 3.我们的黄金规则是：首先优化前端表现，这些东西耗费了用户端响应时间中的80%。
# 具体的优化准则 #
## 1.尽可能使用少的HTTP请求  ##
> 这个是很重要的一条，具体措施是使用Image maps  和Inline Images；合并CSS和脚本代码。
##  2.使用内容分发网络  ##
> 在发布你的动态内容之前发布网站的静态内容，比如使用广泛的Akamai
##  3.增加一个期限头部  ##
> 不仅仅是对图片设置，对于脚本和样式表同样需要设置，在Apache中具体的配置方法如下：

>  ExpiresActive On   ExpiresByType application/x-javascript
> "modification plus 2 years"   ExpiresByType text/css "modification
> plus 5 years"

> 当你修改一个资源的时候，修改资源的名称(自动进行)，给文件名称加一个时间戳，例如img_1385413733.png；同时候将资源纳入版本控制系统中，比如使用CVS个SVN，例如img_1.2.png
##  4.压缩组件（GZIP压缩)  ##
> 当前90% 的浏览器都支持压缩，压缩不仅仅是指HTML，脚本，CSS和XML都可以压缩。 在Apache中修改下面的配置来支持压缩

> Apache 2.x: mod_deflate AddOutputFilterByType DEFLATE text/html
> text/css application/x-javascript

> 对于HTTP请求和响应，表现如下

> HTTP request： Accept-Encoding: gzip, deflate HTTP response:
> Content-Encoding: gzip Vary: Accept-Encoding
##  5.将CSS置为页面的顶部  ##
> 主要原因是CSS样式表会阻塞HTML在IE中的显示(如果没有下载完的话)
##  6.将JS移植页面的底部 Move JS to the bottom  ##
> 主要原因是脚本的执行会阻塞并行下载和阻塞其他元素在IE中的展示
7.避免使用CSS表达式
> 比如下面的例子：

> width: expression(
>              document.body.clientWidth < 600 ?
>           “600px” : “auto” );
> 
> 原因是由于鼠标的移动，按下键等操作事件会是的上述表达式执行多次
##  8. JS和CSS从外部包含  ##
> 可以减少HTML文档的大小，同时增加脚本的重用，但是会增加请求数，HomePage是一个例外，在Homepage中使用Inline和
> PostOnload效果不错，yahoo和goolged首页都这样处理了
##  9.减少DNS的查找  ##
> 一个典型的DNS查询是20－120ms,DNS 查询的时间直接影响到并行下载的效率，尽量减少主机的域名数目。 常用浏览器的缓存时间设置如下：
> IE 
> DnsCacheTimeout: 30 minutes KeepAliveTimeout: 1 minute
> ServerInfoTimeout: 2 minutes

> Firefox
> network.dnsCacheExpiration: 1 minute network.dnsCacheEntries: 20
> network.http.keep-alive.timeout: 5 minutes Fasterfox: 1 hour, 512
> entries, 30 seconds
##  10.精简JS  ##
> 这个主要涉及到JS的编码和优化
##  11.避免重定向  ##
> 通常Web服务器返回的是3XX的状态码，常常是301和302，可以通过添加过期头来缓存重定向
##  12. 移除重复的脚本  ##
> 统计数剧表明20%的网站存在重复的脚本文件，这将直接影响HTTP请求的性能
##  13.关闭ETags功能  ##
> Web服务器返回的实体的唯一标识，主要应用在有条件的Get请求中
> 
> ETag: "c8897e-aee-4165acf0"
>     Last-Modified: Thu, 07 Oct 2004 20:54:08 GMT
>     If-None-Match: "c8897e-aee-4165acf0"   If-Modified-Since: Thu, 07 Oct 2004 20:54:08 GMT
> 
> 如果ETags不匹配，就不能发送304码，对于服务器之间一个单独的实体的ETag总是不同的。在Server和Client的格式分别 如下:
> Apache: inode-size-timestamp   
> IIS: Filetimestamp:ChangeNumber

> 对于服务器多余一台机器的Site，很少返回304状态码。
##  14.是AJAX代码可缓存同时经可能小  ##
> XHR, JSON, iframe, 动态脚本 都是可以被缓存,
> 精简和压缩的比如在Yahoo!中，由于XML标示的地址本是不会经常变动的，可以缓存它，将最后修改时间标记在URL中
。
##  Web性能分析工具  ##
> 1、
> IBM Page Detaile  [http://alphaworks.ibm.com/tech/pagedetailer](http://alphaworks.ibm.com/tech/pagedetailer)

> 2、
> Fasterfox 是Firefox的一个插件，可以详细统计一个网页的载入时间，[http://fasterfox.mozdev.org/](http://fasterfox.mozdev.org/)

> 3、
> LiveHTTPHeaders 是Firefox的一个插件，可以查看HTTP请求头 [http://livehttpheaders.mozdev.org/](http://livehttpheaders.mozdev.org/)

> 4、
> firebug 是Firefox的一个插件, 功能强大，可以多JS和CSS做分析[http://getfirebug.com/](http://getfirebug.com/)

来源：[http://www.cnblogs.com/yangligogogo/articles/1977171.html](http://www.cnblogs.com/yangligogogo/articles/1977171.html)