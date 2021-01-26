---
title: "RESTful"
categories: [ "Design Patterns" ]
tags: [ "restful" ]
draft: false
slug: "restful"
date: "2014-09-22 16:48:00"
---

本文将会简单的介绍什么是REST以及怎样设计REST风格的Web Service。REST这个词首先由大牛Roy Fielding在他的博士论文中提出来，它是一种网络系统设计的架构风格。REST即Representational State Transfer, 对应成中文大概是“表述性状态转移”。

## 为什么是REST
Web由各种各样的资源组成，一个资源可以是任何我们感兴趣的东西。例如，波音公司可以设计一个747资源，用于表示747型号的飞机。我们可以用如下的URL访问这个资源：
	
	http://www.boeing.com/aircraft/747

关于这个URL的解释，原文（不翻译是因为原文更能表达意思）如下：

>A **representation** of the resource is returned (e.g., Boeing747.html). The representation places the client
application in a **state**. The result of the client traversing a hyperlink in Boeing747.html is another resource is accessed. The new representation places the client application into yet another state. Thus, the client application changes (**transfers**) state with each resource representation --> Representational State
Transfer!


<!--more-->


另外，Roy Fielding本人对REST的解释为：
> Representational State Transfer is intended to evoke an image of how a well-designed Web application
behaves: a network of web pages (a virtual state-machine), where the user progresses through an
application by selecting links (state transitions), resulting in the next page (representing the next state of
the application) being transferred to the user and rendered for their use.

单独翻译这三个词的意思可以理解为：Representational即表现出来的资源，即任一种MIME类型；State为客户端的状态，State Transfer即状态转移，通过Hyperlink实现状态转移。

## REST是一种风格，而不是一个标准
需要注意的是，REST不是一个标准，而只是一个设计web应用的风格，或软件的设计模式，同MVC一样。因此，你可以通过理解这种设计风格，从而设计出REST风格的web services, 我们也称RESTful Web Services。如果对其感兴趣，Ruby on rails是非常值得尝试的。

REST使用到如下的标准：

- HTTP
- URL
- XML/HTML/GIF/JPEG/etc(Resource Representations)
- text/xml,text/html,image/gif,image/jpeg, etc(MIME Types)

## REST风格系统
Web本身就是一个REST系统，它用URL来标注每一个网络资源。还有很多的服务，例如搜索，在线字典服务，都可以看成是基于REST的web服务。因此，你一直在使用REST，只是你自己没有意识到。

## 例子
某贸易公司部署了一个web服务，使得客户能够：

- 获得商品列表
- 获得某个商品的详细信息
- 提交一个订单

为了获得商品列表，客户可以使用如下的URL来获取：

	http://www.parts-depot.com/parts

然后，客户可能将获得到如下的文档

	<?xml version="1.0"?>
	<p:Parts xmlns:p="http://www.parts-depot.com" 
	         xmlns:xlink="http://www.w3.org/1999/xlink">
	      <Part id="00345" xlink:href="http://www.parts-depot.com/parts/00345"/>
	      <Part id="00346" xlink:href="http://www.parts-depot.com/parts/00346"/>
	      <Part id="00347" xlink:href="http://www.parts-depot.com/parts/00347"/>
	      <Part id="00348" xlink:href="http://www.parts-depot.com/parts/00348"/>
	</p:Parts>

为了获得商品的详细信息，客户可以使用如下的URL：

	http://www.parts-depot.com/parts/00345

然后，客户将获得如下的文档

	<?xml version="1.0"?>
	<p:Part xmlns:p="http://www.parts-depot.com"   
	        xmlns:xlink="http://www.w3.org/1999/xlink">
	      <Part-ID>00345</Part-ID>
	      <Name>Widget-A</Name>
	      <Description>This part is used within the frap assembly</Description>
	      <Specification xlink:href="http://www.parts-depot.com/parts/00345/specification"/>
	      <UnitCost currency="USD">0.10</UnitCost>
	      <Quantity>10</Quantity>
	</p:Part>

同样，提交一个订单，也将通过类似的过程，只不过换成了HTTP POST方式与服务器进行交互。而每一次的交互过程，都是从一个资源转移到另一个资源，这也即表述性状态转移的大致意思。

还有需要注意的是，这里的URL是逻辑URL，并不代表一个具体的html页面。一个资源只是概念上的实体。因此，这种风格的URL并不会暴露你的实际的技术实现。

## REST Web服务的特点

- Client/Server: a pull-based interaction style: consuming components pull representations.
- 无状态：每次客户端到服务器的请求必须包含全部信息
- 缓存：可以通过缓存机制改善网络效率
- 统一的接口：所有的资源都是通过统一的接口进行访问的（例如：HTTP GET, PUT, DELETE POST）
- 命名的资源：系统由资源组成，资源用URL标识
- 相互联系的资源表述：the representations of the resources are interconnected using URLs, thereby enabling a client to progress from one state to another.
- 分层的组件：intermediaries, such as proxy servers, cache servers, gateways, etc, can be inserted between clients and resources to support performance, security, etc.


我们了解了什么是REST, 并且知道大部分的网络服务都提供了REST API，国内的诸如微博、土豆等，国外的诸如google, twitter等。但是我们如何来具体的使用REST API呢，本文将介绍如何使用Java语言使用第三方REST API。

##　基于Streaming的方式
这种方法比较传统，也是最为直观的开发方式，利用HttpURLConnection建立指向特定URL的连接请求，从而获取JSON字符串。Java API对HttpURLConnection的解释是：
> 每个HttpURLConnection实例都可用于生成单个请求，但是其他实例可以透明地共享连接到HTTP服务器的基础网络。请求后在 HttpURLConnection 的 InputStream 或 OutputStream 上调用 close() 方法可以释放与此实例关联的网络资源，但对共享的持久连接没有任何影响。如果在调用 disconnect() 时持久连接空闲，则可能关闭基础套接字。

因此，我们可以使用如下的方法建立指向特定URL的HTTP连接：

	URL url = new URL("YOUR URL INSERT HERE");
    HttpURLConnection conn = (HttpURLConnection) url.openConnection();
    conn.setRequestMethod("GET");
    conn.setRequestProperty("Accept", "application/json");
    br = new BufferedReader(new InputStreamReader(conn.getInputStream()));


通过以上方法我们可以获得指向该URL的流对象。通过读取该流，我们自然可以获得该URL指向的web资源，即json字符串。流的读取非常简单，我们的想法是逐行读取该流对象，将其加入到一个StringBuffer对象中，最后输出整个StringBuffer字符串即可。

	String output;
    StringBuffer sb = new StringBuffer();
	while ((output = br.readLine()) != null) {
    	sb.append(output);
    }
	String jsonString = sb.toString();//the final result

通过上面的方法我们便得到了该json字符串。既然是json字符串，我们就可以解析该json字符串，从而获取我们想要的数据。对Java而言，我们可以使用第三方的json-lib库，从字符串构造json对象，然后解析json对象。

这种方法，***还有一种简单的写法***：

	URL url = new URL("REST_URL_HERE");
	InputStream is = url.openStream();
	ByteArrayOutputStream os = new ByteArrayOutputStream();
	int retVal;
	while ((retVal = is.read()) != -1) {
	    os.write(retVal);
	}
	final String tweetsString = os.toString();

##　使用Jersey
前面的博客我们简单的介绍了Jersey，Jersey是JSR 311的参考实现，可以用于构造和解析REST API。Jersey的引入可以使问题变的更简单。在使用之前，我们需要引入Jersey库及相关的依赖。

构造Jersey Client, 通过如下的方法建立指定URL的HTTP连接：
	
	javax.ws.rs.client.Client client = javax.ws.rs.client.ClientBuilder.newClient();
    javax.ws.rs.client.WebRarget webTarget = client.target("YOUR_URI").path("CHILD_URI");

通常我们可以将其放在构造的Jersey Client的构造函数中，这样连接的建立工作可以直接在初始化时完成。下面我们只要构造对指向URI的读写方法，可以通过如下的代码完成：
	
    public <T> T find_JSON_REST(Class<T> responseType, String text) throws ClientErrorException {
        WebTarget resource = webTarget;
        resource = resource.queryParam("parameter 1", "parameter value");//set param/value
        resource = resource.queryParam("parameter 2", "parameter value");//set param/value
        return resource.request(javax.ws.rs.core.MediaType.APPLICATION_JSON).get(responseType);
    }

完成以上工作后，我们基本就完成了任务。最后，我们可以编写如下的测试代码。这段代码指定了响应类型为String, 并且传入了一个自定义参数。
	
    /**
     * 
     * @param responseType
     * @param text your self-defined parameter
     * @return
     */
	public static void main(String[] args) {
        TestJerseyClient movieClient = new TestJerseyClient();
        JSONObject jo = JSONObject.fromObject(movieClient.find_JSON_REST(String.class, "YOUR_SELF_DEFINED_PARAMETER"));
        System.out.println(jo.toString());
    }

## 小结

最后，推荐中文的文章，如下：

- 理解RESTful架构 <http://www.ruanyifeng.com/blog/2011/09/restful.html>
- RESTful API设计指南 <http://www.ruanyifeng.com/blog/2014/05/restful_api.html>