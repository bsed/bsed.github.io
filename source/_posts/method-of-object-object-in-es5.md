---
title: ES5 中 Object对象的方法
date: 2015-03-15 13:05:00
updated: 2015-11-07 13:06:01
tags: 
- css
- border
categories: 
- css

---
Object.getPrototypeOf(object):调用对象父类原型上的方法;

Object.getOwnPropertyDescriptor(object, propertyname):获取对象中属性的ECMAScript对象;

Object.defineProperty(object, propertyname, descriptor):将ECMAScript对象设置为对象中的属性.

object.defineProperties(object, descriptors):用 ECMAScript对象 设置为object中多个属性的值.

Object.getOwnPropertyNames(object):返回一个由对象属性名组成的数组(包含不可枚举的)

Object.create(prototype, descriptors):建立一个原型为(prototype)(必需,可为NULL),(descriptors)(可选)为ECMAScript对象的对象.


<!--more-->


Object.seal(object):锁定对象,无法修改对象的属性,无法加入新的属性.并把ECMAScript对象的configurable设置为false;

Object.freeze(object):冻结对象,无法修改对象的属性,无法加入新的属性.

Object.preventExtensions(object):避免加新属性加入对象(Extensible设置为false);

Object.isSealed(object);
Object.isFrozen(object);
Object.isExtensible(object);

判断对象是否为锁定,冻结,不可扩展的.(如果一个对象是冻结的,那其肯定是密封的);

Object.keys(object):返回一个由对象可枚举的属性组成的数组.

http://www.cnblogs.com/blog-zwei1989/archive/2012/10/25/2737282.html
