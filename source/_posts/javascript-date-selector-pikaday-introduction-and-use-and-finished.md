---
title: JavaScript 日期选择器 Pikaday 简介和使用以及汉化
date: 2014-05-12 17:31:00
updated: 2015-02-04 15:03:00
tags: 
- GFW
- 翻墙
categories: 
- default

---
用的最多的日期选择器的 JavaScript 库是基于 jQuery UI 的，但是这样的库在文件大小上是非常大的（压缩和最小化之后都还有50多K），这样是不太适合一些项目的。而今天介绍的 Pikaday 是一个非常简洁但是功能完善，并且样子还算不错的 JavaScript 库。

## Pikaday 介绍 ##
![qrcode-js.png][1]
[Pikaday](https://github.com/dbushell/Pikaday) 是一个 JavaScript 日期选择器，它不依赖于任何 Javascript 库，并且文件大小小于 5K，但是功能却一点不弱，可以进行高级定制。并且样式可以根据 CSS 进行更改选择器的设计，当然默认的样式已经非常不错了。


<!--more-->


## Pikaday 的简单使用 ##
1.首先创建如下的输入框：

    <input type="text" id="datepicker">

2.在页脚加载 Pikaday 的 Javascript 库和 CSS 文件，并调用 Pikaday：

    <link rel="stylesheet" href="http://dbushell.github.com/Pikaday/css/pikaday.css">
    <script src="http://dbushell.github.com/Pikaday/pikaday.js"></script>
    <script>
        var picker = new Pikaday({ field: document.getElementById('datepicker') });
    </script>

如果网页已经加载了 jQuery 库，其中调用代码可以改成下面更简洁的方式：

    var picker = new Pikaday({ field: $('#datepicker')[0] });

以上2步就完成了简单的 Pikaday 的使用。下载：[Pikaday](https://github.com/dbushell/Pikaday)

**但是在使用当中发现有几个限制：**

界面不支持中文。
选择日期格式只能是 Tue Sep 03 2013 这样的格式。
下面就讲解下如何解决这两个问题：

1.首先同样创建如下的输入框：

    <input type="text" id="datepicker">

2.调用时间格式化和本地化 JavaScript 库：Momnet.js：

    <script src="http://cdn.staticfile.org/moment.js/2.2.1/moment.min.js"></script>

3.在页脚加载 Pikaday 的 Javascript 库和 CSS 文件：

    <link rel="stylesheet" href="http://dbushell.github.com/Pikaday/css/pikaday.css">
    <script src="http://dbushell.github.com/Pikaday/pikaday.js"></script>

4.调用和格式化 Pikaday

    <script type="text/javascript">
    $(document).ready(function(){
    
    	var i18n = { // 本地化
    		previousMonth	: '上个月',
    		nextMonth		: '下个月',
    		months			: ['一月','二月','三月','四月','五月','六月','七月','八月','九月','十月','十一月','十二月'],
    		weekdays		: ['周日','周一','周二','周三','周四','周五','周六'],
    		weekdaysShort	: ['日','一','二','三','四','五','六']
    	}
    
    	var datepicker = new Pikaday({ 
    		field:		jQuery('#datepicker')[0],  
    		minDate:	new Date('2000-01-01'), 
    		maxDate:	new Date('2020-12-31'), 
    		yearRange:	[2000,2020],
    		i18n: 		i18n,
    		onSelect: 	function() {
    			var date = document.createTextNode(this.getMoment().format('YYYY-MM-DD') + ' '); //生成的时间格式化成 2013-09-25
    			$('#datepicker').appendChild(date);
    		}
    	});
    
    });
    </script>

5.最后效果：
(略)


  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/793787287.png