---
title: childNodes.length与children.length的区别
date: 2008-12-24 13:43:00
updated: 2015-12-02 13:44:15
tags: 
- java
- lockeliminate
categories: 
- java

---
childNodes.length与children.length的值常不一样。
因为childNodes包含的不仅仅只有html节点，所有属性，文本等都包含在childNodes里面,你可以通过nodeType来判断是哪种类型的节点，只有当nodeType==1时才使元素节点，2是属性节点，3是文本节点。而children.length只计算节点的数量。
包括进行节点长度的计算，节点的移动时都要注意这一点。


<!--more-->


要获得一个元素的文本，比如要获得一个P节点的文本，有的人喜欢使用innerHTML，更改也是一样，其实这不太好，因为使用innerHTML浏览器会认为你要插入的东西是一段标签文本，而你只想插入一段纯文本，这就导致浏览器试图去解析你的那段纯文本，这会造成不必要的资源浪费。所以建议使用element.firstChild.nodeValue,为什么是firstChild呢？因为childNodes[0]就是文本节点，不信你可以看看它的nodeType。
