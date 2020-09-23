---
title: JavaScript 的事件监听兼容处理
date: 2011-10-09 11:51:00
updated: 2015-11-07 11:58:22
tags: 
- gulp
- move
- task
categories: 
- js

---
在事件监听处理方面，IE提供了`attachEvent`和`detachEvent`两个接口，而Firefox提供的是`addEventListener`和`removeEventListener`。最简单的兼容性处理就是封装这两套接口：

    function addEvent(elem, eventName, handler) {
    　　if (elem.attachEvent) {
    　　　　elem.attachEvent("on" + eventName, handler);
    　　} else if (elem.addEventListener) {
    　　　　elem.addEventListener(eventName, handler, false);
    　　}
    }
    
    function removeEvent(elem, eventName, handler) {
    　　if (elem.detachEvent) {
    　　　　elem.detachEvent("on" + eventName, handler);
    　　} else if (elem.removeEventListener) {
    　　　　elem.removeEventListener(eventName, handler, false);
    　　}
    }


<!--more-->


然而，上面两个函数还没把问题完全解决。Firefox下，事件处理函数中的this指向被监听元素本身，而在IE下则不然。要解决这个问题，我首先想到的是`prototype`框架中的`function.prototype.bind`（下面简称bind）：

    function addEvent(elem, eventName, handler) {
    　　if (elem.attachEvent) {
    　　　　elem.attachEvent("on" + eventName, handler.bind(elem));
    　　} else if (elem.addEventListener) {
    　　　　elem.addEventListener(eventName, handler, false);
    　　}
    }

这样做的结果是，再也无法移除事件处理函数。原因在于，移除事件处理函数时需要传入该函数的引用，而bind方法返回的是一个新函数而不是`handler`本身，且这个新函数的引用并没有保存下来。既然如此，只要把bind返回的引用给保存下来就好了。而存储结构就是个关键点。

首先，一个页面中有任意个元素，所以每个元素都要有一个集合，每个元素所有事件处理函数的引用就存放在这个集合中。

    element.events = { };

其次，一个元素有多种事件（click、mouseover、mouseout等），同一个函数可以添加为多种事件的处理函数，所以每种事件的存储也必须是独立的。

    element.events.click = {};
    element.events.mouseover = {};
    ...

最后，为每个事件处理函数添加一个唯一标识，以便在集合找到它。最简单的标识方法是数据库中常用的自动编号（递增）。

结合以上三点，编写一个生成事件代理的函数：

    var eventId = 0;
    function delegate(elem, eventName, handler) {
    　　var events = elem.events = elem.events || {}, // 创建事件集合
    　　　　id = handler.eventId = handler.eventId || ++eventId; // 生成事件标识
    　　events[eventName] = events[eventName] || {}; // 创建某种事件的集合
    
    　　var trueHandler = function(e) { // 真正被添加的事件处理函数
    　　　　handler.call(elem, e);
    　　};
    
    　　events[eventName][id] = trueHandler; // 记下函数的引用
    }

相对应地，还要写一个获取事件代理的函数：

    function getDelegate(elem, eventName, handler) {
    　　try { return elem.events[eventName][handler.eventId]; } catch (e) {}
    }

这里用try...catch的原因是，避免 `elem.events` 和 `elem.events[eventName]`未创建时出错。

最后addEvent和removeEvent改进如下：

    function addEvent(elem, eventName, handler) {
    　　handler = delegate(elem, eventName, handler);
    　　if (elem.attachEvent) {
    　　　　elem.attachEvent("on" + eventName, handler);
    　　} else if (elem.addEventListener) {
    　　　　elem.addEventListener(eventName, handler, false);
    　　}
    }
    
    function removeEvent(elem, eventName, handler) {
    　　handler = getDelegate(elem, eventName, handler);
    　　if (elem.detachEvent) {
    　　　　elem.detachEvent("on" + eventName, handler);
    　　} else if (elem.removeEventListener) {
    　　　　elem.removeEventListener(eventName, handler, false);
    　　}
    }

