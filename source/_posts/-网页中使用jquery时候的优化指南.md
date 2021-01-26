---
title: " 网页中使用jquery时候的优化指南"
categories: [ "JS" ]
tags: [ "jquery" ]
draft: false
slug: "jquery-time-optimization-guide-for-use-in-web-pages"
date: "2015-10-25 09:54:00"
---

jQuery现在已经非常流行，百度新首页中也已经开始使用jQuery，今天总结下怎么书写更好的jQuery代码使jQuery代码更好、更快的执行，希望本篇jQuery教程一改大家以前不合理的做法。
 
## 1.正确引用jQuery

尽量在body结束前才引入jQuery，而不是在head中（解释:因为jquery文件比较大，script标签是阻塞加载）
借助第三方提供的CDN来引入jQuery，同时注意当使用第三方CDN出现问题时，要引入本地的jQuery文件。
如果在</body>前引入script文件的话，就不用写document.ready了，因为这时执行js代码时DOM已经加载完毕了。

*js代码:*

    <body>  
        <script src="http://lib.sinaapp.com/js/jquery11/1.8/jquery.min.js"></script>  
        <script>window.jQuery || document.write('<script src="jquery1.8.min.js"></script>')</script>
    </body>
	</body>

## 2.优化jQuery选择器
高效正确的使用jQuery选择器是熟练使用jQuery的基础，而掌握jQuery选择器需要一定的时间积累，我们开始学习jQuery时就应该注意选择器的使用。
 
*js代码:*

    <div id="nav" class="nav">  
        <a class="home" href="http://xxxx.com">web开发</a>  
        <a class="articles" href="http://xxxx.com/">web开发</a>  
    </div>

如果我们选择class为home的a元素时，可以使用下边代码：

*js代码:*

    $('.home');  //1  
    $('#nav a.home');  //2  
    $('#nav').find('a.home');  //3  

方法1会使jQuery在整个DOM中查找class为home的a元素，性能可想而知。
方法2为要查找的元素添加了上下文，在这里变为查找id为nav的子元素，查找性能得到了很大提升。
方法3使用了find方法，它的速度更快，所以方法三最好。
关于jQuery选择器的性能优先级，ID选择器快于元素选择器，元素选择器快于class选择器。因为ID选择器和元素选择器是原生的JavaScript操作，而类选择器不是，大家顺便可以看下find context 区别，find() children区别。

## 3.缓存jQuery对象

缓存jQuery对象可以减少不必要的DOM查找，关于这点大家可以参考下缓存jQuery对象来提高性能。

## 4.正确使用事件委托

事件委托可以使事件更好的执行，在动态添加的元素上绑定事件也需要委托来实现，在新版本的jQuery中推荐大家使用on来给元素绑定一个或多个事件的处理函数。

    <table id="t">  
        <tr>  
            <td>我是单元格</td>  
        </tr>  
    </table>  

比如我们要在上边的单元格上绑定一个单击事件，不注意的朋友可能随手写成下边的样子：

*js代码:*

    $('#t').find('td').on('click', function () {  
        $(this).css({ 'color': 'red', 'background': 'yellow' });  
    }); 

这样会为每个td绑上事件，在为100个单元格绑定click事件的测试中，两者性能相差7倍之多，好的做法应该是下边写法：
*js代码:*

    $('#t').on('click', 'td', function () {  
        $(this).css({ 'color': 'red', 'background': 'yellow' });  
    });  

## 5.精简jQuery代码

如在上述代码中我们对jQuery代码进行了适当的合并，类似的还有.attr()方法等，我们没有写成下边的方式： 

*js代码:*

    $('#t').on('click', 'td', function () {  
        $(this).css({ 'color': 'red', 'background': 'yellow' });  
    });  

## 6.减少DOM操作

刚开始使用jQuery时可能会频繁的操作DOM，这是相当耗费性能的。如我们要在body中动态输出一个表格，一些朋友会这样写：

*js代码:*

    var $t = $('body');  
    $t.append('<table>');  
    $t.append('<tr><td>1</td></tr>');  
    $t.append('</table>');  

 好的做法：
*js代码:*

    $('body').append('<table><tr><td>1</td></tr></table>');

这样在拼接完table串后再添加到body中，对DOM的操作只需一次。群里以前有朋友就因为这个导致在IE下输出时出现问题，而关于字符串的拼接可以参考下最快创建字符串的方法。

## 7.不使用jQuery

原生函数总是最快的，这点不难理解，在代码书写中我们不应该忘记原生JS。
就先总结这几条吧，每条建议并不难理解，但总结全面的话还是要花费不少时间的。如在减少代码段中，如果需要根据条件从数组中得到新数组时，可以使用$.grep() 方法，如果你在使用jQuery时有自己心得的话，欢迎在留言中和大家分享！


## 8.缓存变量

DOM遍历是昂贵的，所以尽量将会重用的元素缓存。

    // 糟糕
    
    h = $('#element').height();
    $('#element').css('height',h-20);
    
    // 建议
    
    $element = $('#element');
    h = $element.height();
    $element.css('height',h-20);

## 9.避免全局变量

jQuery与javascript一样，一般来说,最好确保你的变量在函数作用域内。

    // 糟糕
    
    $element = $('#element');
    h = $element.height();
    $element.css('height',h-20);
    
    // 建议
    
    var $element = $('#element');
    var h = $element.height();
    $element.css('height',h-20);

## 10.使用匈牙利命名法

在变量前加$前缀，便于识别出jQuery对象。

    // 糟糕
    
    var first = $('#first');
    var second = $('#second');
    var value = $first.val();
    
    // 建议 - 在jQuery对象前加$前缀
    
    var $first = $('#first');
    var $second = $('#second'),
    var value = $first.val();

## 使用 Var 链（单 Var 模式）

将多条var语句合并为一条语句，我建议将未赋值的变量放到后面。

    var $first = $('#first'),
          $second = $('#second'),
          value = $first.val(),
          k = 3,
          cookiestring = 'SOMECOOKIESPLEASE',
          i,
          j,
          myArray = {};

## 11.请使用’On’

在新版jQuery中，更短的 on(“click”) 用来取代类似 click() 这样的函数。在之前的版本中 on() 就是 bind()。自从jQuery 1.7版本后，on() 附加事件处理程序的首选方法。然而，出于一致性考虑，你可以简单的全部使用 on()方法。

    // 糟糕
    
    $first.click(function(){
        $first.css('border','1px solid red');
        $first.css('color','blue');
    });
    
    $first.hover(function(){
        $first.css('border','1px solid red');
    })
    
    // 建议
    $first.on('click',function(){
        $first.css('border','1px solid red');
        $first.css('color','blue');
    })
    
    $first.on('hover',function(){
        $first.css('border','1px solid red');
    })

## 12.精简javascript

一般来说,最好尽可能合并函数。

    // 糟糕
    
    $first.click(function(){
        $first.css('border','1px solid red');
        $first.css('color','blue');
    });
    
    // 建议
    
    $first.on('click',function(){
        $first.css({
            'border':'1px solid red',
                'color':'blue'
        });
    });

## 13.链式操作

jQuery实现方法的链式操作是非常容易的。下面利用这一点。

    // 糟糕
    
    $second.html(value);
    $second.on('click',function(){
        alert('hello everybody');
    });
    $second.fadeIn('slow');
    $second.animate({height:'120px'},500);
    
    // 建议
    
    $second.html(value);
    $second.on('click',function(){
        alert('hello everybody');
    }).fadeIn('slow').animate({height:'120px'},500);

## 14.维持代码的可读性

伴随着精简代码和使用链式的同时，可能带来代码的难以阅读。添加缩紧和换行能起到很好的效果。

    // 糟糕
    
    $second.html(value);
    $second.on('click',function(){
        alert('hello everybody');
    }).fadeIn('slow').animate({height:'120px'},500);
    
    // 建议
    
    $second.html(value);
    $second
        .on('click',function(){ alert('hello everybody');})
        .fadeIn('slow')
        .animate({height:'120px'},500);

## 15.选择短路求值

短路求值是一个从左到右求值的表达式，用 &&（逻辑与）或 || （逻辑或）操作符。

    // 糟糕
    
    function initVar($myVar) {
        if(!$myVar) {
            $myVar = $('#selector');
        }
    }
    
    // 建议
    
    function initVar($myVar) {
        $myVar = $myVar || $('#selector');
    }

## 16.选择捷径

精简代码的其中一种方式是利用编码捷径。

    // 糟糕
    
    if(collection.length > 0){..}
    
    // 建议
    
    if(collection.length){..}

## 17.繁重的操作中分离元素

如果你打算对DOM元素做大量操作（连续设置多个属性或css样式），建议首先分离元素然后在添加。

    // 糟糕
    
    var $container = $("#container"),
        $containerLi = $("#container li"),
        $element = null;
    
    $element = $containerLi.first(); 
    //... 许多复杂的操作
    
    // better
    
    var $container = $("#container"),
        $containerLi = $container.find("li"),
        $element = null;
    
    $element = $containerLi.first().detach(); 
    //... 许多复杂的操作
    
    $container.append($element);

## 18.熟记技巧

你可能对使用jQuery中的方法缺少经验,一定要查看的文档，可能会有一个更好或更快的方法来使用它。

    // 糟糕
    
    $('#id').data(key,value);
    
    // 建议 (高效)
    
    $.data('#id',key,value);

## 19.使用子查询缓存的父元素

正如前面所提到的，DOM遍历是一项昂贵的操作。典型做法是缓存父元素并在选择子元素时重用这些缓存元素。

    // 糟糕
    
    var container = $('#container'),
        $containerLi = $('#container li'),
        $containerLiSpan = $('#container li span');
    
    // 建议 (高效)
    
    var $container = $('#container '),
        $containerLi = $container.find('li'),
        $containerLiSpan= $containerLi.find('span');

## 20.避免通用选择符

将通用选择符放到后代选择符中，性能非常糟糕。

    // 糟糕
    
    $('.container > *'); 
    
    // 建议
    
    $('.container').children();

## 21.避免隐式通用选择符

通用选择符有时是隐式的，不容易发现。

    // 糟糕
    
    $('.someclass :radio'); 
    
    // 建议
    
    $('.someclass input:radio');

## 22.优化选择符

例如，Id选择符应该是唯一的，所以没有必要添加额外的选择符。

    // 糟糕
    
    $('div#myid'); 
    $('div#footer a.myLink');
    
    // 建议
    $('#myid');
    $('#footer .myLink');

## 23.避免多个ID选择符

在此强调，ID 选择符应该是唯一的，不需要添加额外的选择符，更不需要多个后代ID选择符。

    // 糟糕
    
    $('#outer #inner'); 
    
    // 建议
    
    $('#inner');

## 24.坚持最新版本

新版本通常更好：更轻量级，更高效。显然，你需要考虑你要支持的代码的兼容性。例如，2.0版本不支持ie 6/7/8。

## 25.摒弃弃用方法

关注每个新版本的废弃方法是非常重要的并尽量避免使用这些方法。

    // 糟糕 - live 已经废弃
    
    $('#stuff').live('click', function() {
          console.log('hooray');
    });
    
    // 建议
    $('#stuff').on('click', function() {
          console.log('hooray');
    });
    // 注：此处可能不当，应为live能实现实时绑定，delegate或许更合适

## 26.利用CDN

谷歌的CND能保证选择离用户最近的缓存并迅速响应。（使用谷歌CND请自行搜索地址，此处地址以不能使用，推荐jquery官网提供的CDN）。

必要时组合jQuery和javascript原生代码

如上所述，jQuery就是javascript，这意味着用jQuery能做的事情，同样可以用原生代码来做。原生代码（或 vanilla）的可读性和可维护性可能不如jQuery，而且代码更长。但也意味着更高效（通常更接近底层代码可读性越差，性能越高，例如：汇编，当然需要更强大的人才可以）。牢记没有任何框架能比原生代码更小，更轻，更高效（注：测试链接已失效，可上网搜索测试代码）。

鉴于vanilla 和 jQuery之间的性能差异，我强烈建议吸收两人的精华，使用（可能的话）和jQuery等价的原生代码。

## 27. 维持代码的可读性

伴随着精简代码和使用链式的同时，可能带来代码的难以阅读。添加缩紧和换行能起到很好的效果。

    // 糟糕
    $second.html(value);
    $second.on('click',function(){
        alert('hello everybody');
    }).fadeIn('slow').animate({height:'120px'},500);
    
    // 建议
    $second.html(value);
    $second
        .on('click',function(){ alert('hello everybody');})
        .fadeIn('slow')
        .animate({height:'120px'},500);

## 28.繁重的操作中分离元素

如果你打算对DOM元素做大量操作（连续设置多个属性或css样式），建议首先分离元素然后在添加。

    // 糟糕
    var 
        $container = $("#container"),
        $containerLi = $("#container li"),
        $element = null;
    
    $element = $containerLi.first(); 
    //... 许多复杂的操作
    
    // better
    var 
        $container = $("#container"),
        $containerLi = $container.find("li"),
        $element = null;
    
    $element = $containerLi.first().detach(); 
    //... 许多复杂的操作
    $container.append($element);

最后忠告

最后，我记录这篇文章的目的是提高jQuery的性能和其他一些好的建议。如果你想深入的研究对这个话题你会发现很多乐趣。记住，jQuery并非不可或缺，仅是一种选择。思考为什么要使用它。DOM操作？ajax？模版？css动画？还是选择符引擎？或许javascript微型框架或jQuery的定制版是更好的选择。

