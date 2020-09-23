---
title: JavaScript中判断对象是否为空
date: 2010-10-13 20:01:00
updated: 2015-10-06 20:03:41
tags: 
- windows
- git
categories: 
- linux

---
判断JavaScript对象是否是一个空的对象，可以遍历对象。若对象拥有任意的实例属性，则对象非空。

    /**
     * Is #obj empty?
     * @param {Object} obj object to be detected.
     */
    function isEmptyObject(obj) {
        for(var prop in obj) {
            if(obj.hasOwnProperty(prop)) {
                return false;
            }
        }
        return true;
    }


<!--more-->


若ECMAScript 5的Object.keys可用，则可以判断Object.keys返回数组的长度：

    function isEmptyObject(obj) {
        if (typeof Object.keys === "function") {
            return Object.keys(obj).length === 0;
        }
        // 遍历对象判断
    }

*参考资料：*

[How do I test for an empty Javascript object from JSON?]()