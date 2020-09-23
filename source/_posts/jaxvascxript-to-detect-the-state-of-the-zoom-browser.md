---
title: javascript检测浏览器的缩放状态
date: 2014-11-12 19:12:00
updated: 2016-11-20 09:18:21
tags: 
- javascript
- prototype
categories: 
- js

---
这里所说的缩放不是指浏览器大小的缩放，而是指浏览器网页内容的百分比缩放（按Ctrl和+号键或者-号键的缩放）。

检测这种缩放有很种方法，QQ空间都通过flash来检测浏览器是否处于缩放。这里提供javascript的方法来检测浏览器的缩放。

对于 IE6，就直接无视了，因为 IE6 只能对文本进行缩放。

先来说说浏览器提供的标准检测接口，window.devicePixelRatio 是设备上物理像素和设备独立像素的比例，该属性就可以用于检测网页是否被缩放了。在普通的 PC 浏览器上，在默认无缩放的情况下其默认值是 1。可惜这么方便的属性目前只有 Firefox 支持。


<!--more-->


好吧，接下来该说说 IE 的处理方法了。IE 提供了 window.screen.deviceXDPI 和 window.screen.logicalXDPI 两个属性，deviceXDPI 就是对应的设备上的物理像素，而 logicalXDPI 就是对应了设备独立像素的比例。估计标准的检测接口也只是基于 IE 这种方法的一种改进。这两个属性在 windows XP+ 以上的系统上的默认值都是 96，因为系统默认的就是 96dpi 。

对于 webkit 和 opera，它们都支持 window.outerWidth 和 window.innerWidth 这两个属性。outerWidth 返回的是窗口元素的外部实际宽度，innerWidth 返回的是窗口元素的内部实际宽度，这两个宽度都包含了滚动条在内的宽度。

有了这些属性基本就可以搞定 PC 浏览器上常见的浏览器了。实现代码如下：

detectZoom 函数的返回值如果是 100 就是默认缩放级别，大于 100 则是放大了，小于 100 则是缩小了。
```javascript
function detectZoom (){ 
	var ratio = 0,
		screen = window.screen,
		ua = navigator.userAgent.toLowerCase();

	if (~ua.indexOf('firefox')) {
		if (window.devicePixelRatio !== undefined) {
			ratio = window.devicePixelRatio;
		}
	}
	else if (~ua.indexOf('msie')) {	
		if (screen.deviceXDPI && screen.logicalXDPI) {
			ratio = screen.deviceXDPI / screen.logicalXDPI;
		}
	}
	else if (window.outerWidth !== undefined && window.innerWidth !== undefined) {
		ratio = window.outerWidth / window.innerWidth;
	}
	
	if (ratio){
		ratio = Math.round(ratio * 100);
	}
	
	// 360安全浏览器下浏览器最大化时诡异的outerWidth和innerWidth不相等
	if (ratio === 99 || ratio === 101) {
		ratio = 100;
	}
	
	return ratio;
};
```

原文: [http://www.javascript100.com/?p=1296](http://www.javascript100.com/?p=1296)