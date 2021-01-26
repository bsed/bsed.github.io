---
title: "xml解析库的效率"
categories: [ "Linux" ]
tags: [ "xml" ]
draft: false
slug: "the-efficiency-of-xml-parsing-library"
date: "2013-11-20 17:43:00"
---

简单比较一下一些XML解析库的效率。
样张文件：slideMaster1.xml
样张大小：11.2 MB (11,845,597 bytes)
样张来源：Microsoft PowerPoint 2010另存一个*.pptx的大文件，将slideMaster1.xml解压出来。


<!--more-->


编译release配置，数据在内存中进行XML解析。
| 库 | 方式 | 耗时（秒）|
| -- | --- | -------- |
| qt-4.8.0 | DOM | 21.927686 |
| qt-4.8.0 | SAX | 9.757670 |
| libxml2 | DOM | 31.828191 |
| libxml2 | SAX | 0.512124 |
| xerces-c-3.1.1 | DOM | 0.694201 |
| xerces-c-3.1.1 | SAX | 0.668720 |
| tinyxml-2.6.2 | DOM | 5.093720 |
| pugixml-1.2 | DOM | 0.421485 |
| rapidxml-1.13 |DOM | 0.200251 |