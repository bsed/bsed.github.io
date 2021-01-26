---
title: "Http的内容实体介绍"
categories: [ "Java" ]
tags: [ "java","http" ]
draft: false
slug: "http-content-entities"
date: "2010-08-26 11:44:00"
---

接下来几篇博客讲写一些关于`Http`实体的内容。

Http的请求报文和响应报文可以携带内容实体，也就是http的真正的内容部分。那些头信息都是给程序看或者说给程序员看的，内容实体才是真正给用户看的，或者说是我们真正需要的数据内容。

实体是可选的，可以有也可以没有。

HttpClient这个开源项目把实体分成三种类型，分类是根据实体的来源分的。

1.流式实体：从相应的中获得的流式的内容，流式实体不可以重复取出。

2.自我包含式实体：从内存中或者其他链接获得，一般是包含在请求中。可以重复生成。（这个我不明白，有明白的网友可以告诉我一声）

3.包装式：直接从另一个实体获得。

重复实体是对于包含式实体俩说的，就是可以重复读取多次，例如：StringEntity,ByteArrayEntity

下面几篇将会介绍实体的使用。

**http补充介绍**：[http://kb.cnblogs.com/page/119118/](http://kb.cnblogs.com/page/119118/)

## 什么是 HTTP Headers？
### 什么是HTTP Headers

HTTP是“Hypertext Transfer Protocol”的所写，整个万维网都在使用这种协议，几乎你在浏览器里看到的大部分内容都是通过http协议来传输的，比如这篇文章。

HTTP Headers是HTTP请求和相应的核心，它承载了关于客户端浏览器，请求页面，服务器等相关的信息。
图片丢失:>

*示例*

当你在浏览器地址栏里键入一个url，你的浏览器将会类似如下的http请求：


    GET /tutorials/other/top-20-mysql-best-practices/ HTTP/1.1
    Host: net.tutsplus.com
    User-Agent: Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US; rv:1.9.1.5) Gecko/20091102 Firefox/3.5.5 (.NET CLR 3.5.30729)
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,**;q=0.8
    Accept-Language: en-us,en;q=0.5
    Accept-Encoding: gzip,deflate
    Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7Keep-Alive: 300
    Connection: keep-aliveReferer: http://localhost/test.php
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 43
    first_name=John&last_name=Doe&action=Submit

这里有三个需要注意的地方：

 1. 第一行的路径已经变为简单的 /foo.php , 已经没了查询字符串。
 2. 新增了 Content-Type 和 Content-Lenght 头部，它提供了发送信息的相关信息.
 3. 所有数据都在headers之后，以查询字符串的形式被发送.

POST方式的请求也可用在AJAX，应用程序，cURL … 之上。并且所有的文件上传表单都被要求使用POST方式。

### HEAD：接收头部信息

HEAD和GET很相似，只不过HEAD不接受HTTP响应的内容部分。当你发送了一个HEAD请求，那就意味着你只对HTTP头部感兴趣，而不是文档本身。

这个方法可以让浏览器判断页面是否被修改过，从而控制缓存。也可判断所请求的文档是否存在。

例如，假如你的网站上有很多链接，那么你就可以简单的给他们分别发送HEAD请求来判断是否存在死链，这比使用GET要快很多。

### HTTP响应结构

当浏览器发送了HTTP请求之后，服务器就会通过一个HTTP response来响应这个请求。如果不关心内容，那么这个请求看起来会是这样的：
图片丢失:>

第一个有价值的信息就是协议。目前服务器都会使用 HTTP/1.x 或者 HTTP/1.1。

接下来一个简短的信息代表状态。代码200意味着我们的请求已经发送成功了，服务器将会返回给我们所请求的文档，在头部信息之后。

我们都见过“404”页面。当我向服务器请求一个不存在的路径时，服务器就用用404来代替200响应我们。

余下的响应内容和HTTP请求相似。这些内容是关于服务器软件的，页面/文件何时被修改过，mime type 等等…

同样，这些头部信息也是可选的。

### HTTP状态码

 - 200 用来表示请求成功.
 - 300 来表示重定向.
 - 400 用来表示请求出现问题.
 - 500 用来表示服务器出现问题.

### 200 成功（OK）

前文已经提到，200是用来表示请求成功的。

### 206 部分内容（Partial Content）

如果一个应用只请求某范围之内的文件，那么就会返回206.

这通常被用来进行下载管理，断点续传或者文件分块下载。

### 404 没有找到（Not Found）

图片丢失:>

很容易理解

### 401 未经授权（Unauthorized）

受密码保护的页面会返回这个状态。如果你没有输入正确的密码，那么你就会在浏览器中看到如下的信息：

图片丢失:>
注意这只是受密码保护页面，请求输入密码的弹出框是下面这个样子的：
图片丢失:>

### 403 被禁止（Forbidden）

如果你没有权限访问某个页面，那么就会返回403状态。这种情况通常会发生在你试图打开一个没有index页面的文件夹。如果服务器设置不允许查看目录内容，那么你就会看到403错误。

其它一些一些方式也会发送权限限制，例如你可以通过IP地址进行阻止，这需要一些htaccess的协助。

    order allow,deny
    deny from 192.168.44.201
    deny from 224.39.163.12
    deny from 172.16.7.92
    allow from all

### 302（或307）临时移动（Moved Temporarily） 和 301 永久移动（Moved Permanently）

这两个状态会出现在浏览器重定向时。例如，你使用了类似 bit.ly 的网址缩短服务。这也是它们如何获知谁点击了他们链接的方法。

302和301对于浏览器来说是非常相似的，但对于搜索引擎爬虫就有一些差别。打个比方，如果你的网站正在维护，那么你就会将客户端浏览器用302重定向到另外一个地址。搜索引擎爬虫就会在将来重新索引你的页面。但是如果你使用了301重定向，这就等于你告诉了搜索引擎爬虫：你的网站已经永久的移动到了新的地址。

### 500 服务器错误（Internal Server Error）
![2011102010543919.png][6]

这个代码通常会在页面脚本崩溃时出现。大部分CGI脚本都不会像PHP那样输出错误信息给浏览器。如果出现了致命的错误，它们只会发送一个500的状态码。这时需要查看服务器错误日志来排错。

## 完整的列表

你可以在[这里](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes)找到完整的HTTP 状态码说明。

### HTTP Headers 中的 HTTP请求

现在我们来看一些在HTTP headers中常见的HTTP请求信息。

所有这些头部信息都可以在PHP的$_SERVER数组中找到。你也可以用getallheaders() 函数一次性获取所有的头部信息。

### Host
　　一个HTTP请求会发送至一个特定的IP地址，但是大部分服务器都有在同一IP地址下托管多个网站的能力，那么服务器必须知道浏览器请求的是哪个域名下的资源。

`Host: rlog.cn`

只是基本的主机名，包含域名和子级域名。

在PHP中，可以通过$_SERVER['HTTP_HOST'] 或 $_SERVER['SERVER_NAME']来查看。

### User-Agent
 

    User-Agent: Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US; rv:1.9.1.5) Gecko/20091102 Firefox/3.5.5 (.NET CLR 3.5.30729)

这个头部可以携带如下几条信息：

 - 浏览器名和版本号.
 - 操作系统名和版本号.
 - 默认语言.

这就是某些网站用来收集访客信息的一般手段。例如，你可以判断访客是否在使用手机访问你的网站，然后决定是否将他们引导至一个在低分辨率下表现良好的移动网站。

在PHP中，可以通过` $_SERVER['HTTP_USER_AGENT'] `来获取`User-Agent`

     if ( strstr($_SERVER['HTTP_USER_AGENT'],'MSIE 6') ) 
      {
      echo "Please stop using IE6!";
      }

### Accept-Language
 

    Accept-Language: en-us,en;q=0.5

这个信息可以说明用户的默认语言设置。如果网站有不同的语言版本，那么就可以通过这个信息来重定向用户的浏览器。

它可以通过逗号分割来携带多国语言。第一个会是首选的语言，其它语言会携带一个“q”值，来表示用户对该语言的喜好程度（0~1）。

在PHP中用 $_SERVER["HTTP_ACCEPT_LANGUAGE"] 来获取这一信息。


    if (substr($_SERVER['HTTP_ACCEPT_LANGUAGE'], 0, 2) == 'fr') 
    {
    header('Location: http://french.mydomain.com');
    }

### Accept-Encoding

    Accept-Encoding: gzip,deflate

大部分的现代浏览器都支持gzip压缩，并会把这一信息报告给服务器。这时服务器就会压缩过的HTML发送给浏览器。这可以减少近80%的文件大小，以节省下载时间和带宽。

在PHP中可以使用 $_SERVER["HTTP_ACCEPT_ENCODING"] 获取该信息。 然后调用ob_gzhandler()方法时会自动检测该值，所以你无需手动检测。


    // enables output buffering
    // and all output is compressed if the browser supports itob_start('ob_gzhandler'); 

### If-Modified-Since

如果一个页面已经在你的浏览器中被缓存，那么你下次浏览时浏览器将会检测文档是否被修改过，那么它就会发送这样的头部：

     If-Modified-Since: Sat, 28 Nov 2009 06:38:19 GMT

如果自从这个时间以来未被修改过，那么服务器将会返回“304 Not Modified”，而且不会再返回内容。浏览器将自动去缓存中读取内容

在PHP中，可以用`$_SERVER['HTTP_IF_MODIFIED_SINCE']` 来检测。

    // assume $last_modify_time was the last the output was updated
    // did the browser send If-Modified-Since header?
    if(isset($_SERVER['HTTP_IF_MODIFIED_SINCE'])) { 
    // if the browser cache matches the modify time
    if ($last_modify_time == strtotime($_SERVER['HTTP_IF_MODIFIED_SINCE'])) { 
    // send a 304 header, and no content
    header("HTTP/1.1 304 Not Modified"); 
    exit;
    }
    }

还有一个叫Etag的HTTP头信息，它被用来确定缓存的信息是否正确，稍后我们将会解释它。

## Cookie

顾名思义，他会发送你浏览器中存储的Cookie信息给服务器。


    Cookie: PHPSESSID=r2t5uvjq435r4q7ib3vtdjq120; foo=bar

它是用分号分割的一组名值对。Cookie也可以包含session id。

在PHP中，单一的Cookie可以访问$_COOKIE数组获得。你可以直接用$_SESSION array获取session变量。如果你需要session id，那么你可以使用session_id()函数代替cookie。

    echo $_COOKIE['foo']; 
    // output: bar
    echo $_COOKIE['PHPSESSID']; 
    // output: r2t5uvjq435r4q7ib3vtdjq120
    session_start(); echo session_id(); 
    // output: r2t5uvjq435r4q7ib3vtdjq120 

### Referer

顾名思义, 头部将会包含referring url信息。

例如，我访问Nettuts+的主页并点击了一个链接，这个头部信息将会发送到浏览器：

    Referer: http://net.tutsplus.com/

在PHP中，可以通过 $_SERVER['HTTP_REFERER'] 获取该值。

     if (isset($_SERVER['HTTP_REFERER'])) 
    { 
    $url_info = parse_url($_SERVER['HTTP_REFERER']); 
    // is the surfer coming from Google?
    if ($url_info['host'] == 'www.google.com') { 
    parse_str($url_info['query'], $vars); 
    echo "You searched on Google for this keyword: ". $vars['q'];}} 
    // if the referring url was:// http://www.google.com/search?source=ig&hl=en&rlz=&=&q=http+headers&aq=f&oq=&aqi=g-p1g9
    // the output will be:
    // You searched on Google for this keyword: http headersYou may have noticed the word “referrer” is misspelled as “referer”. Unfortunately it made into the official HTTP specifications like that and got stuck.

### Authorization

当一个页面需要授权，浏览器就会弹出一个登陆窗口，输入正确的帐号后，浏览器会发送一个HTTP请求，但此时会包含这样一个头部：

    Authorization: Basic bXl1c2VyOm15cGFzcw==

包含在头部的这部分信息是base64 encoded。例如，base64_decode(‘bXl1c2VyOm15cGFzcw==’) 会被转化为 ‘myuser:mypass’ 。

在PHP中，这个值可以用$_SERVER['PHP_AUTH_USER'] 和 $_SERVER['PHP_AUTH_PW'] 获得。

更多细节我们会在WWW-Authenticate部分讲解。

### HTTP Headers 中的 HTTP响应
现在让我了解一些常见的HTTP Headers中的HTTP响应信息。

在PHP中，你可以通过 header() 来设置头部响应信息。PHP已经自动发送了一些必要的头部信息，如载入的内容，设置 cookies 等等… 你可以通过 headers_list() 函数看到已发送和将要发送的头部信息。你也可以使用headers_sent()函数来检查头部信息是否已经被发送。

### Cache-Control

w3.org 的定义是：“The Cache-Control general-header field is used to specify directives which MUST be obeyed by all caching mechanisms along the request/response chain.” 其中“caching mechanisms” 包含一些你ISP可能会用到的 网关和代理信息。

*例如：*

    Cache-Control: max-age=3600, public



