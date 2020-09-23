---
title: 手机浏览器无法上下拖动，鼠标可以滚动
date: 2014-02-07 20:12:00
updated: 2017-02-05 20:14:21
tags: 
- webpack
- fetch-js
categories: 
- js

---
html页面在chrome手机模拟器，手机真实浏览器环境下，无法上下拖动(滑动)，但是在模拟器下鼠标可以滚动，
发现：

```javascript
//这个作用: 禁掉相应的事件[禁止了touchmove事件] 
document.addEventListener('touchmove', function(e) {
 e.preventDefault(); 
}, false) 
```

 1. `addEventListener()` 方法用于向指定元素添加事件句柄。
 2. 使用 `removeEventListener()` 方法来移除 `addEventListener()` 方法添加的事件句柄。