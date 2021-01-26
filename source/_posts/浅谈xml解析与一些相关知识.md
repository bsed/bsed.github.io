---
title: "浅谈xml解析与一些相关知识"
categories: [ "Java" ]
tags: [ "java","xml" ]
draft: false
slug: "xml-analysis-and-some-related-knowledge"
date: "2013-10-24 16:43:00"
---

## XML
可扩展标记语言，即eXtensible Markup Language，XML被广泛用来作为跨平台之间交互数据的形式，主要针对数据的内容，通过不同的格式化描述手段（XSLT，CSS等）可以完成最终的形式表达（生成对应的HTML，PDF或者其他的文件格式）。

XML设计用来传送及携带数据信息，不用来表现或展示数据，HTML语言则用来表现数据，所以XML用途的焦点是它说明数据是什么，以及携带数据信息。

## XML解析
XML的解析通常分为两种方式，分别是基于树的解析和基于事件的解析。下面简单介绍这两种解析方式。

1.基于树的解析-DOM解析器：它是W3C推荐标准，它将整个XML文档读入内存中，缺点是当遇到较大的XML文档时，DOM解析可能会变得较慢且消耗内存。

2.基于事件的解析-SAX解析器：SAX即Simple API for Xml，它顺序读取XML文档，当它遇到文档中重要部分时会出发事件串行操作，一个节点被处理后就被丢弃了，并且不会再被处理，整个文档并不一次性地读入内存，而是随着解析的进行逐步读取。


<!--more-->


## JAXP
另一个值得一说的是就是JAXP，即Java API for XML Processing。JAXP是SUN公司提供的为处理XML文档而开发的工具包，JAXP使得XML的解析难度大大降低，并且易于掌握。严格说来，**JAXP 是 API，但更准确地说是抽象层**。它没有提供解析 XML 的新方法，没有添加到 SAX 或 DOM，也没有为 Java 和 XML 处理提供新功能。

JAXP 为两种流行的 Java 和 XML API 提供了有用的插入层 (pluggability layer)。它使得代码具有开发商中立性，并允许进行解析器之间的更改，而无需重新编译解析代码

JAXP为解析XML提供了三种基本接口：

- DOM接口：DocumentBuilder，它构建一个内存中的Document表示。
- SAX接口：SAX解析器被称作SAXParser，与DOM解析器不同，SAX解析器并不创建XML文档的内存表示，因此要更快使用更少的内存。而是，SAX解析器通过调用回调方法将XML文档结构告知客户端。
- StAX接口

这里并不会详细的介绍如何使用这些接口，而只是提供一个概要，具体如何使用可以参考相关的文档。

## XML约束

- DTD:简单，功能少
- XML Schema：复杂，功能大而全


###XML文档的显示技术
- CSS：XML文档是支持使用CSS进行装饰的，通常使用如下的方式引入CSS文件

		<?xml-stylesheet type="text/css" href="style.css" ?>

- XSLT:	可以用来将一个源XML文档和转换成另一个XML文档（结果树）。例如，XSLT可以将XML转换成XHTML，然后显示在网页浏览器中。

## XPath
XPath语言是一门专门用于在XML文档中查找信息的语言，查找后可以得到如下结果：

- 一个单独的节点；
- 一组节点；
- 一个布尔值；
- 一个浮点数；
- 一个字符串。
	
XPath将XML文档看成由节点构成的层次树，每棵树包括：

- 元素节点；
- 属性节点；
- 文本节点；
- 处理指令；
- 注释
- 命名空间。

XML的使用简单使用教程可以参考如下文档：
<http://www.zvon.org/xxl/XPathTutorial/General_chi/examples.html>

XPath能够在xml文件中，快速定位需要元素，无需从根元素一个一个的导航到需要的子元素。例如：
		
	/list/book/name
其可快速定位到
	
	<list>
		<book>
			<name>XXXX</name>

中的name节点，其中list为根节点

## Dom4j
Dom4j is an easy to use, open source library for working with XML, XPath and XSLT on the Java platform using the Java Collections Framework and with full support for DOM, SAX and JAXP.(dom4j official site)

关于Dom4j的使用，具体参见相关文档，这里只是介绍一下，dom4j在项目中的使用时非常普遍的，得益于其简单而强大的解析能力。下载dom4j的文件，导入如下的jar包到项目中即可使用：

- dom4j-1.x.x.jar
- /lib/jaxen-1.1-beta-6.jar  //for xpath

这里简单的介绍下dom4j的常用API：

SAXReader类：

	//构造SAXReader
	SAXReader sr = new SAXReader();
	//加载文件
	Document doc = sr.read(File File("book"));
	//Document对象代表XML文件在内存中的印象
	Element root = doc.getRootElement();//取得文档的根元素
				
	Element.getName()
	Element.elements() //取得该元素下的所有直接子元素
	Element.elementText("str") //从一个元素导航到另一个元素，并取出该元素的文本
	Element.element("str"); //导航到另一个元素

	Element.attributeValue("str"); //取得该元素对应的属性

XMLWriter类：
	
	OutputFormat format = OutputFormat.createPrettyPrint(); //使用缩进格式后的XML文件
	OutputStream os = new FileOutputStream(new File("test.xml"));//创建文件输出流，指定输出的文件对象
	XMLWriter xw = new XMLWriter(os, format);//构造XML写入器
	xw.write(doc);//将内存中的文件写入硬盘
	xw.close(); //只需关闭XMLWriter流即可，可以理解为

在Dom4j中使用Xpath:（需导入jaxen.jar包）

	String xpath = "//单价";
	List<Element> elementList = doc.selectNodes(xpath);//直接定位所有的值为“单价”的所有元素，存放在一个List中
		
	Doc.selectSingleNode() 只返回找到的第一个元素