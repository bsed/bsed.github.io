---
title: "JS低版本IE兼容filter "
categories: [ "JS" ]
tags: [ "ie","filter" ]
draft: false
slug: "js-low-version-ie-compatible-with-filter"
date: "2013-11-08 22:04:00"
---

filter() 方法利用所有通过指定函数测试的元素创建一个新的数组，并返回。
例子：筛选排除掉所有的小值

    function isBigEnough(element) {
      return element >= 10;
    }
    var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
    // filtered is [12, 130, 44]

兼容旧环境（Polyfill）


<!--more-->


    if (!Array.prototype.filter){
      Array.prototype.filter = function(fun /*, thisArg */){
        "use strict";
    
        if (this === void 0 || this === null)
          throw new TypeError();
    
        var t = Object(this);
        var len = t.length >>> 0;
        if (typeof fun !== "function")
          throw new TypeError();
    
        var res = [];
        var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
        for (var i = 0; i < len; i++){
          if (i in t){
            var val = t[i];
    
            // NOTE: Technically this should Object.defineProperty at
            //       the next index, as push can be affected by
            //       properties on Object.prototype and Array.prototype.
            //       But that method's new, and collisions should be
            //       rare, so use the more-compatible alternative.
            if (fun.call(thisArg, val, i, t))
              res.push(val);
          }
        }
    
        return res;
      };
    }

## 浏览器兼容性

| Feature | Chrome | Firefox (Gecko) | Internet Explorer | Opera | Safari |
| -- | -- | -- | -- | --- | --    |
| 基本支持 | (Yes) | 1.5 (1.8) | 9 | (Yes) | (Yes) |
