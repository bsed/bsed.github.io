---
title:  ;(function ( $, window, document, undefined )
date: 2013-05-22 11:05:00
updated: 2016-05-20 11:04:31
tags: 
- php
- self
- this
categories: 
- php

---
在jQuery中我们经常看到以下这段代码：
```
;(function ( $, window, document, undefined ){

//函数体内具体代码

})(jQuery, window,document);
```

<!--more-->


**首先说说非常值得提倡的几点：**

 - 1、代码最前面的分号，可以防止多个文件压缩合并以为其他文件最后一行语句没加分号，而引起合并后的语法错误。
 - 2、匿名函数(function(){})();：由于JavaScript执行表达式是从圆括号里面到外面，所以可以用圆括号强制执行声明的函数。避免函数体内和外部的变量冲突。
 - 3、$实参:$是jquery的简写，很多方法和类库也使用$,这里$接受jQuery对象，也是为了避免$变量冲突，保证插件可以正常运行。
 - 4、window, document实参分别接受window, document对象，window,
   document对象都是全局环境下的，而在函数体内的window, document其实是局部变量，不是全局的window,
   document对象。这样做有个好处就是可以提高性能，减少作用域链的查询时间，如果你在函数体内需要多次调用window 或
   document对象，这样把window 或
   document对象当作参数传进去，这样做是非常有必要的。当然你如果你的插件用不到这两个对象，那么就不用传递这两个参数了。
 - 5、最后剩下一个undefined形参了，那么这个形参是干什么用的呢，看起来是有点多余。undefined在老一辈的浏览器是不被支持的，直接使用会报错，js框架要考虑到兼容性，因此增加一个形参undefined

