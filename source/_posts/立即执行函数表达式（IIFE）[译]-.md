---
title: "立即执行函数表达式（IIFE）[译] "
categories: [ "JS" ]
tags: [ "javascript","iife","closure" ]
draft: false
slug: "immediate-execution-function-expression-iife"
date: "2013-12-16 20:08:00"
---

> 原文：[Immediately-Invoked Function Expression
> (IIFE)](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)
> 
> 作者：”Cowboy” Ben Alman

或许你没有注意到，我是一个对术语比较在意的人。所以，在听到许多次JavaScript「自执行匿名函数」（self-executing anonymous function）或者「自调用匿名函数」（self-invoked anonymouse function）这两个流行但令人误解的术语，我觉得得将我的想法写下来。

本文实际上除了提供详细关于这个模式的原理之外，还推荐了我们之后如何称呼这个模式。当然，你可以跳过关于这个推荐的内容，直接查看「立即执行函数表达式」的例子，但是我建议你完整地阅读本文。

请注意，本文并非是那种「我就是正确的，而你是错误」的论调。我真诚的希望帮助人们了解潜在的复杂的概念，并且深感使用一致并且精确的术语是一种非常有效的促进人们相互理解的方法。

## 那么，到底本文旨在何处？


<!--more-->


在JavaScript中，每一个函数被调用时都会创建一个执行上下文（execution context）。定义在函数内部的变量和函数都只能在这个执行上下文的内部访问到，所以函数提供了一种创建私有成员的便捷的方法。

    // makeCounter返回了另外一个内部函数。
    // 而这个内部函数可以访问私有变量i，所以这个内部函数实际上拥有一个特权（访问内部私有变量）
    function makeCounter() {
        // `i`变量仅在`makeCounter`函数内部有效
        var i = 0;
    
        return function () {
            console.log(++i);
        };
    }
    
    // 注意，`counter` 和 `counter2` 拥有各自的`i`
    var counter = makeCounter();
    counter(); // logs: 1
    counter(); // logs: 2
    
    var counter2 = makeCounter();
    counter2(); // logs: 1
    counter2(); // logs: 2
    
    i; // ReferenceError: i未定义（i仅在makeCounter中有效）

[译注]：[JSFiddle例子链接](http://jsfiddle.net/cEhxD/)

在很多情况下，不论makeWhatever函数返回的是什么样的值，你希望这个值在每次返回都是相同的，或者，你根本不需要函数返回一个值。

## 问题的本质

假若你定义了一个函数：`function foo () {}` 或者 `var foo = function () {}`，你都获得了一个函数的引用变量。可以在在这个变量使用括号操作符（parenthese）来调用函数，即：`foo()`。

    // 像底下定义的函数可以在函数名之后放置括号()，比如foo()来调用。
    // 而foo仅仅是一个函数表达式`function(){ /* code */ }`的引用。
    var foo = function () { /* code */}
    
    // 那么是否可以在函数表达式之后直接使用()来调用？
    function () { /* code */ }(); // SyntaxError: Unexpected token (

上面代码执行后就会抛出一个错误（catch）。当脚本解释器在全局命名空间或者函数内部遇到关键字`function`的时候，默认地，解释器认为这是一个**函数声明语句**（[function declaraction](https://developer.mozilla.org/en/JavaScript/Reference/Statements/function)），而非**函数表达式**（[function expression](https://developer.mozilla.org/en/JavaScript/Reference/Operators/Special/function)）。如果没有明确地告知解释器function(){}()是一个函数表达式，解释器将认为这是一个函数声明，并且声明没有指定函数的名字。由于函数声明时必需为指定函数的名称，所以解释器抛出**语法错误**（[SyntaxError](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/SyntaxError)）。

## 题外话：函数，括号以及语法错误

有趣的是，既然是由于缺乏函数名称而导致语法错误，那么只要指定一个函数名字不就通过了？解释器依然会抛出一个语法错误提示，但却是由于不同的原因。当括号放置在函数表达式后，此括号即为括号运算符，表示调用函数；然而当括号放置在语句之后意味着分离括号前面与括号中的内容，此时括号仅仅做为分组表示（即用于改变运算的优先关系）。

    // 照理说底下语句是有效。但解释器认为这依然是一个函数声明语句。
    // 后面的括号是分组操作，由于括号中没有内容，所以解释器抛出异常。
    function foo(){ /* code */ }(); // SyntaxError: Unexpected token )
    
    // 既然由于括号内没有内容导致异常，那么只要在括号内加入内容不就可以了...
    // 但是函数依然没有执行
    function foo(){ /* code */ }( 1 );
    
    // 上面的语句等价与一个函数声明，紧接这一个与函数完全无关的表达式。
    // 这是解释器所看到的：
    function foo(){ /* code */ }
    
    ( 1 );

你可以访问Dmitry A. Soshnikove对于此运算内容翔实的文章：[ECMA-262-3 in detail. Chapter 5. Functions`(http://dmitrysoshnikov.com/ecmascript/chapter-5-functions/#question-about-surrounding-parentheses)。


## 立即执行函数表达式（IIFE）

幸运的是，可以简单地修复上述的语法错误问题。被广为接受的修复方式是使用括号明确告诉解释器这个是一个函数表达式。因为在JavaScript中，括号无法包含语句。因此，当解释器遇到括号中的function关键字时，将认为这是一个函数表达式，而非函数声明。

    // 这两种写法都可以立即执行一个函数表达式，同时创建了一个私有作用域
    (function(){ /* code */ }()); // Crockford 推荐这种写法
    (function(){ /* code */ })(); // 但是这种也一样有效
    
    // 由于括号或者强迫操作符用于让解释器可正确区分函数表达式与函数声明，所以在解释器可以正确区分的地方，括号就可以省略
    // 不过请查看下面的重要说明
    var i = function(){ return 10; }();
    true && function(){ /* code */ }();
    0, function(){ /* code */ }();
    
    // 如果不在意函数的返回值或者不在意代码的可读性，使用一元操作符亦是有效的
    !function(){ /* code */ }();
    ~function(){ /* code */ }();
    -function(){ /* code */ }();
    +function(){ /* code */ }();
    
    // 以下是另外一种形式（@kuvos提供）。
    new function(){ /* code */ }
    new function(){ /* code */ }() // 括号仅在传参时需要

## 关于上述代码中括号的一个重要说明

虽然在上述那些括号可有可无（因为解释器可以正确地识别他们是函数表达式），但还是建议在赋值语句中带上括号，作为一种不成文的约定。

括号在这种情况下表示函数将会立即被执行，同时，执行结果返回的函数执行的结果而非函数本身。通过这个约定，碰到一个函数表达式时就没有必要滚动到函数表达式的末尾（函数可能非常长）去查看是否立即执行了这个函数。

根据以往的经验，编写清晰的代码在技术上可以避免JavaScript解释器抛语法错误异常，同时也可以避免遇到「屎一样的BUG」。

## 使用闭包（closures）保存状态

实参可以传递给函数名（通过函数声明），也可以传递给立即调用的函数表达式。同时，函数（outer）可以在内部定义另外一个函数（inner），此时内部函数（inner）可以访问外部函数(outer)的传递进来参数和变量（这种关系被称为「闭包」）。结合上述的两种特性，我们可以使用「立即执行函数表达式」来锁定变量值并保存状态。

如果想要了解更多关于「闭包」的内容，请访问：[Closures explained with JavaScript](http://skilldrick.co.uk/2011/04/closures-explained-with-javascript/)。

    // 底下例子不会按照你的预期工作，因为`i`的值没有被锁定。
    // 相反，当任意一个链接被点击时（在循环结束之后），弹出的值中i的值都等于elems.length。
    // 这是因为在链接被点击的时候（循环之后），`i`的值就是elems.length
    var elems = document.getElementsByTagName('a');
    for (var i = 0; i < elems.length; i++) {
        elems[i].addEventListener('click', function (e) {
            e.preventDefault();
            alert('I am link #' + i);
        }, 'false');
    }
    
    // 这个例子如预期工作，因为在IIFE内部，`i`的值被锁定为`lockedInIndex`。
    // 即使在循环之后`i`的值为元素的总个数，但是在IIFE中，`lockedInIndex`的值永远都是IIFE被调用的时候传入的`i`的值。
    // 也就是说循环时每次传入的`i`(0, 1...)都在IIFE调用是固定为`lockedInIndex`，不会被改变。
    // 所以每个链接被点击时都获取到了期望的`i`值
    var elems = document.getElementsByTagName('a');
    for (var i = 0; i < elems.length; i++) {
        (function (lockedInIndex) {
            elems[i].addEventListener('click', function (e) {
                e.preventDefault();
                alert('I am link #' + lockedInIndex);
            }, 'false');
        })(i);
    }
    
    // 也可以采用这种方式。在click的回掉函数上使用IIFE锁定`i`的值并返回一个函数。
    // 两种方式都可以按照预期的想法工作。但是上面的例子更易阅读。
    var elems = document.getElementsByTagName('a');
    for (var i = 0; i < elems.length; i++) {
        elems[i].addEventListener('click', (function (lockedInIndex) {
            return function (e) {
                e.preventDefault();
                alert('I am link #' + lockedInIndex);
            };
        })(i), 'false');
    }

注意上述代码中的最后两个例子，`lockedInIndex` 可以正确的使用`i`的值。使用`lockedInIndex`作为立即执行函数表达式的参数令代码看起来更易理解。

立即执行函数表达式的另外一个优点在于，由于立即执行函数表达式并未使用标识符来命名，即未命名或者匿名的，所以可以不污染全局命名空间的情况使用。

## 那么术语「自执行匿名函数」哪里不对？

在上文中已经多次提到我所建议的术语「立即执行函数表达式」，或者简写为「IIFE」，但是并未明确说明。我将他念成「iffi」（亦非？）。

什么是「立即执行函数表达式」？顾名思义，她是一个被立即执行的函数表达式。

我很希望看到JavaScript社区接受「立即执行函数表达式」（IIFE），在他们的文章中使用这个术语。因为我觉得IIFE让这个概念更加清晰，也因为术语「自执行匿名函数」并不准确：

    // 这是一个「自执行函数」。函数在内部调用本身，形成递归。
    function foo() { foo(); }
    
    // 这是一个「自执行匿名函数」。因为没有命名，所以在函数内部使用arguments.callee来调用自身。
    var foo = function() { arguments.callee(); };
    
    // 这应该就是一个所谓的「自执行匿名函数」了，但仅限于`foo`标识符引用这个函数。
    // 如果foo指向了其他的值，那么这个就成为了「曾经是自执行匿名函数」了。
    var foo = function() { foo(); };
    
    // 这个语句被称为「自执行匿名函数」。但是她并非自执行，因为她根本没有调用她本身。这是一个立即执行函数。
    (function(){ /* code */ }());
    
    // 为一个函数表达式指定名字在调试的时候异常有由，在调用栈里可以查找到。
    // 然后一旦指定名字，这就是一个非匿名函数了。
    (function foo(){ /* code */ }());
    
    // IIFE同样可以调用自身，但或许这不是一个非常有用的形式。
    (function(){ arguments.callee(); }());
    (function foo(){ foo(); }());
    
    // 最后一提，以下这个语句在BlackBerry5中会异常，因为在一个具名函数内部，此变量引用的函数还未生成是undefined。有趣吧？
    (function foo(){ foo(); }());

希望上述的例子可以验证「自执行」这个词是令人误解的。虽然函数一样是执行了，但并非是函数执行她本身。同时，「匿名」也无须特别指出，因为一个**立即执行函数表达式**可以匿名也可以具有名字。最后，我比较喜欢「invoked」而非「executed」在于押韵，我想「IIFE」看起来念起来比「IEFE」顺。

这就是我的想法。

由于`arguments.callee`在ECMAScript 5 strict mode中已被弃用，所以从技术上说一个自执行匿名函数是不可能实现的。

## 最后的题外话：模块模式（Module Pattern）

当我在论述函数表达式的时候，可以漏掉了模块模式。若你对JavaScript里面的模块模式不熟悉的话，那么查看文中第一个例子，那个例子就是接近于模块模式。之所以说接近，是由于模块模式返回的是一个对象，而第一个例子返回的是函数。若第一个例子返回值改成一个对象，也就通常实现单例模式（Singleton Pattern）的方法，如下代码所示：

    // 创建一个立即执行的匿名函数表达式，并将函数的返回值赋予一个变量。
    // 与第一例子相比，这个方法略去了`makeWhatever`中间函数。
    
    // 就像上面重要提示中所述，尽管在这个例子中，外面的括号是非必需的。
    // 但加上括号可以明确这是以立即执行的函数，将函数的结果赋予变量，而非将函数赋予变量。
    var counter = (function () {
        var i = 0;
    
        return {
            get: function () {
                return i;
            },
            set: function (val) {
                i = val;
            },
            increment: function () {
                return ++i;
            }
        };
    }());
    
    // `counter`是一个带有成员的对象，在此例中她的成员都是函数。
    
    counter.get(); // 0
    counter.set(3);
    counter.increment(); // 4
    counter.increment(); // 5
    
    counter.i; // undefined (`i` 并非`counter`的成员)
    i; // ReferenceError: i未定义 (仅存在于匿名函数表达式形成的私有作用域中，即闭包)

[译注] [例子的JSFiddle链接](http://skilldrick.co.uk/2011/04/closures-explained-with-javascript/http://jsfiddle.net/kDBdu/)

模块模式不仅仅强大并且简洁明了。使用很少的代码就可以有效地将方法和属性封装起来，与此同时不污染全局命名空间以及创建私有作用域。

## 扩展阅读

希望本文可以解答你的一些疑虑。当然，若阅读完本文之后你的疑问比之前更多了，你可以阅读以下列表的文章以探索更多关于函数以及模块模式：

[ECMA-262-3 in detail. Chapter 5. Functions - Dmitry A. Soshinikov](http://dmitrysoshnikov.com/ecmascript/chapter-5-functions/#question-about-surrounding-parentheses)
[Functions and function scope](https://developer.mozilla.org/en/JavaScript/Reference/Functions_and_function_scope) - Mozilla Developer Network
[Named function expressions](http://kangax.github.com/nfe/) - Named function expressions - Juriy “kangax” Zaytsev
[JavaScript Module Pattern: In-Depth](http://www.adequatelygood.com/2010/3/JavaScript-Module-Pattern-In-Depth) - JavaScript Module Pattern: In-Depth - Ben Cherry
[Closures explained with JavaScript](http://skilldrick.co.uk/2011/04/closures-explained-with-javascript/) - Nick Morgan
最后感谢 Asen Bozhilov 和 John David Dalton 的技术建议以及Nick Morgan的深刻见解。如果你由任何想法，请在评论里发表，谢谢！