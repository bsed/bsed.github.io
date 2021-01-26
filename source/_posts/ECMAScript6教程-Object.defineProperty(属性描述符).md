---
title: "ECMAScript6教程 Object.defineProperty(属性描述符)"
categories: [ "JS" ]
tags: [ "javascript","ecmascript6" ]
draft: false
slug: "ecmascript6-tutorial-objectdefineproperty-attribute-descriptor"
date: "2015-11-07 12:37:00"
---

属性描述符是ES5中新增的概念，其作用是给对象的属性增加更多的控制。
## Object.defineProperty

要研究属性描述符，首先要谈谈 `Object.defineProperty` 方法。这个方法的作用是给对象定义新属性或修改已存在的属性。其原型如下：

    Object.defineProperty(obj, prop, descriptor)

使用示例：

    var obj = { };
    Object.defineProperty(obj, 'attr', { value: 1 });

上面一段代码给obj对象增加了一个名为attr的属性，值为1。相当于：

    var obj = { };
    obj.attr = 1;

相比起来，Object.defineProperty 的写法看似更为复杂。但是，它最大的奥秘在于其第三个参数。

## 数据描述符


<!--more-->


假设我们希望attr是一个只读属性，就可以加上 writable 数据描述符：

    var obj = { };
    Object.defineProperty(obj, 'attr', {
        value: 1,
        writable: false
    });
    console.log(obj.attr);
    obj.attr = 2; // fail
    console.log(obj.attr);

执行以上程序可以发现，两次打印出来的attr的值都是1，也就是说对属性的写入失败。然而，这样的结果会有点莫名其妙，因为赋值语句的执行没有异常，却失败了，试想如果在大片的代码中出现这样的问题，就很难排查出来。事实上，只要以严格模式运行代码，就会产生异常：

    'use strict'; // 进入严格模式
    var obj = { };
    Object.defineProperty(obj, 'attr', {
        value: 1,
        writable: false
    });
    obj.attr = 2;  // throw exception

下面再来看看另一个数据描述符 `enumerable` ，它可以控制属性是否能被枚举。如果只是简单地定义一个属性，这个属性是可以在for...in循环中被枚举出来的：

    var obj = { };
    obj.attr = 1;
    for (var i in obj) { console.log(obj[i]); }

enumerable 可以将其“藏”起来：

    var obj = { };
    Object.defineProperty(obj, 'attr', {
        value: 1,
        enumerable: false
    });
    for (var i in obj) { console.log(obj[i]); }

执行上面一段代码，会发现控制台什么也没输出，因为此时attr属性无法被枚举了。

讲到这里，大家可能有一个疑问，属性描述符能否被修改？比方说一个只读属性是否可以再次定义为可写？其实这取决于另一个数据描述符 configurable ，它可以控制属性描述符能否被更改。

    var obj = { };
    Object.defineProperty(obj, 'attr', {
        value: 1,
        writable: false,
        configurable: true
    });
    Object.defineProperty(obj, 'attr', {
        writable: true
    });
    obj.attr = 2;

上面一段代码先把attr定义为只读属性，然后又重新定义为可写。所以对attr的写入是成功的。
存取描述符

## 存取描述符类似面向对象中的get/set访问器。

    var obj = { };
    Object.defineProperty(obj, 'attr', {
        set: function(val) { this._attr = Math.max(0, val); },
        get: function() { return this._attr; }
    });
    obj.attr = -1;
    console.log(obj.attr); // 0

在上面一段代码中，对attr的访问事实上变成了对_attr的访问，而且在set函数中限制了最小值为0。
## 获取属性描述符

前面所述都是设置属性描述符，那如何获取已设置的描述符呢？Object.getOwnPropertyDescriptor 可以完成此项工作。

    var obj = { };
    Object.defineProperty(obj, 'attr', {
        value: 1,
        writable: false,
        configurable: true
    });
    var desc = Object.getOwnPropertyDescriptor(obj, 'attr');
    console.dir(desc);

## 对象控制

前面说的 `Object.defineProperty` ，其操作的是对象的属性，而下面说的三个方法则直接操作对象。

**Object.preventExtensions** 可以使对象无法拥有新的属性：

    var obj = { };
    obj.attr = 1;
    Object.preventExtensions(obj);
    obj.attr2 = 2; //fail

Object.defineProperty 可以使对象仅剩属性值可以修改（如果属性为只读，则连属性值都无法修改）：

    var obj = { };
    obj.attr = 1;
    Object.seal(obj);
    obj.attr = 1.5;
    delete obj.attr; // fail

Object.freeze 可以使对象完全无法被修改：

    var obj = { };
    obj.attr = 1;
    Object.freeze(obj);
    obj.attr = 1.5; // fail
    obj.attr2 = 2; //fail

然后大家可能又会问，怎么知道某个对象是否曾经被preventExtensions、seal或者freeze呢？答案就是分别调用 `Object.isExtensible` 、 `Object.isSealed` 、 `Object.isFrozen` ，这三个函数的用法比较简单，就不再累赘了。


总的来说，通过属性描述符可以进一步严格控制对象，加强程序逻辑的严谨性，唯一不足的就是，ES5在IE9里面才基本实现（IE9还不支持严格模式），考虑到国内IE8份额还比较高的情况，这套东西目前只能在移动端浏览器和Node.js里面用了。


