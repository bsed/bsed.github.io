---
title: 如何理解JavaScript闭包 [JS基础增强]
date: 2019-03-22 06:22:00
updated: 2019-09-17 13:58:14
tags: 
- promise
- JS基础增强
categories: 
- js

---
引用维基百科：在计算机科学中，`闭包`（英语：Closure），又称`词法闭包`（Lexical Closure）或`函数闭包`（function closures），引用了自由变量的函数。这个被引用的自由变量将和这个函数一同存在，即使已经离开了创造它的环境也不例外。

从维基百科对闭包的解释来看，我们可以这么认为：定义在闭包中的函数，可以记住它被创建时的环境。

闭包是JavaScript非常重要的一个语言特性，在理解闭包之前，我们先来看看JavaScript中的变量以及变量作用域。

> 变量与变量作用域


<!--more-->


## 变量的声明
JavaScript是大小写敏感的，使用Unicode字符集。在JavaScript中，语句被称为statements,并用分号分隔（；）。在程序中，使用变量来为值命名，变量的名称称为identifiers,它需要遵守一定的规则，在JavaScript语言中，一个标识符(identifier)必须以字母、下划线(_)或者美元($)符号开头；后续的字符可以包含数字(0~9)。关于变量的声明有三种：

 - var:使用关键字var声明变量，例如：var x =
   666,同时可以将变量初始化为一个值，这个语法也可以用来声明局部和全局变量。值得注意的是,如果对变量直接赋值，例如：x =
   2333,这样会声明一个全局变量并会导致JavaScript编译时产生一个严重警告，所以要尽量避免这种声明方式。
 - let:使用关键字let声明了一个块范围作用域变量，例如：let = 999, 同时将变量初始化为一个值。
 - const:使用const关键字可以声明一个只读的常量，例如π。

## 2.对变量求值

使用var或者let声明变量时，如果未赋值，会被设定一个默认值undefined，在程序中访问一个未初始化(未声明)的变量，会抛出ReferenceError异常：
```javascript
var a;
console.log("a的值是：" + a);//输出-> a的值是：undefined
console.log("b的值是：" + b);//输出-> Uncaught ReferenceError: b is not defined
```
undefined值在布尔类型环境中会被当做false，例如下面的代码：
```javascript
var a;
if(a){
    console.log("a的值是：" + a);
}else{
    console.log("a的值是：" + a + ",证明undefined在布尔环境中被当做false。");
}
//以上代码执行后输出-> a的值是：undefined,证明undefined在布尔环境中被当做false。
```
undefined值在数值型环境中会被转换为NaN，例如下面的代码：
```javascript
var a;
consoloe.log(a+666);//输出 ->NaN
```
另外，对一个null的变量求值，空值null会在数值型环境中被当做0，在布尔类型环境中会被当做false。

### 3.变量的作用域

在函数之外声明的变量是全局变量，它的值在整个程序都可以访问和修改，在函数内部声明的变量是局部变量，每当函数执行时，就会创建和摧毁该变量，并且无法通过函数之外的任何代码访问该变量。变量的参数也属于局部变量。
```javascript
var a = 666;
function test() {
    var b = 999;
    console.log('a的值是：' + a);
}
test();//a的值是：666
console.log('b的值是：' + b);//ReferenceError: b is not defined
```
关于let和const声明的块范围变量，本文暂不作讨论。

## 4.变量提升

JavaScript的变量声明具有变量提升(hoisting)机制，JavaScript引擎在执行的时候，会把所有变量的声明都提升到当前作用域的最前面。如果变量是局部变量，则提升到函数的最前面，如果变量是全局变量，则提升到全局作用域的开头。看下面一段代码：
```javascript
var a = 'hello';
(function(){
    console.log(a);
    var a = 'javascript'
})();
```
这段代码的运行结果是：undefined。从运行结果来看，我们可以发现两点，一是function作用域里的变量v遮盖了上层作用域变量v；二是在function作用域内，变量v的声明被提升了。也就是说，上面的代码等价于：
```javascript
var a = 'hello';
(functiton(){
    var v;
    console.log(v);
    v = 'world';
})();
```
当前作用域内的的变量和函数的声明都会提升到作用域的最前面，另外函数表达式不会被提升，这也是函数表达式与函数声明的区别。看以下代码：
```javascript
(function(){
    f1();//Uncaught ReferenceError: f1 is not defined
    f2();
    var f1 = function(){};
    function f2(){}
})();
```
## 5.作用域链

当代码在一个环境中执行时，会创建变量对象的一个作用域链(scope chain)。作用域链的用途，是保证对执行环境有权访问的所有变量和函数的有序访问。作用域链的前端，始终都是当前执行的代码所在环境的变量对象。如果这个环境是一个函数，则将其活动对象作为变量对象。

每一个函数都有自己的执行环境，当执行流进一个函数时，函数环境就会被推入一个环境栈中，而在函数执行之后，栈将其环境弹出，把控制权返回给之前的执行环境，这个栈也就是作用域链。

看下面一段代码：
```javascript
var name = 'javascript-closures';
function test(){
    var name = 'javascript';
    function f1(){
        var name = 'closures';
        console.log(name);
    }
    function f2(){
        console.log(name);
    }
    f1();
    f2();
}
test();
```
这段代码执行，当执行到f1()时，此时的作用域链是：`f1()->test()->window` , 很显然在f1函数作用域内，声明并赋值了变量name,所以这时控制台将输出：closures;当执行f2时，此时的作用域链则是：f2->test()->window,由于在f2内部没有声明变量name,所以解释器会向上一级作用域查找这个变量，直到找到该变量的标示符或不再存在父作用域了（若仍没找到该变量，则返回undefined），很显然f2的执行结果是：javascript。

## 理解闭包

简单回顾了一下关于JavaScript变量的基础知识后，接下来开始谈谈我对闭包的理解。当然，在前面讲到作用域链的时候，其实已经使用到闭包的特性了。

什么是闭包呢？其实闭包就是：一个函数和它所引用的外部变量组成的实体。由于外部变量被引用了，所以它的生命被延长。外层函数执行完，按理说它的变量是要全部销毁的，但由于闭包的存在，之后执行此内部函数，通过闭包仍能访问到外层函数的变量，这也是闭包的特点。闭包的本质是：内部函数创建时，会保存在当前环境的作用域链(内部属性[[scope]])中，并且函数被保留下来，引擎不会对外层环境的变量对象进行销毁。

简单来说，闭包就是指有权访问另外一个函数作用域中的变量的函数。首先它是一个函数，其次能访问另外一个函数作用域的变量。来看看下面一个例子：
```javascript
function getWeChat(){
    var wechat = 'wechatnumber';
    console.log(wechat);
}
function displayWeChat(){
    console.log(wechat);
}
```
很显然上面的代码执行displayWeChat的时候会报错，在displayWeChat函数内部并不能访问到getWeChat函数内部的变量wechat,但假如就是想要别人的微信号(wechat)呢？可以对上面的代码进行如下改动，就可以欢快地拿到别人的wechat了：
```javascript
function getWeChat() {
    var wechat = 'wechatnumber';
    function displayWeChat() {
        console.log(wechat);
    }
    return displayWeChat;
}
var getWeChatNumber = getWeChat();
getWeChatNumber();//此时getWeChatNumber其实就是一个闭包了
```
从上面的例子可以看出，关于闭包，有三个特点：

闭包可以访问当前函数以外的变量
看下面一段代码，html结构如下：
```html
<div id="list">
    <li>项目一</li>
    <li>项目二</li>
    <li>项目三</li>
    <li>项目四</li>
</div>
```
JavaScript代码如下：
```javascript
function bindClick(){
    var oList = document.getElementById('list');
    var aLi = oList.getElementsByTagName('li');
    for(var i = 0;i<aLi.length;i++){
        aLi[i].onclick = function() {
            alert('You click the ' + i + ' li tag.');
        }
    }
}
bindClick();
```
上述代码原本想要实现的功能是点击相应的li时，弹出提示用户点击的是第几个li,但这段js代码运行后的结果是：点击每个li标签，弹出的都是相同的结果”You click the 4 li tag”,这是什么原因呢，其实也就是作用域导致的问题，我们可以通过闭包，实现我们想要的结果，对js代码进行如下修改：
```javascript
function bindClick(){
    var oList = document.getElementById('list');
    var aLi = oList.getElementsByTagName('li');
    for(var i = 0;i<aLi.length;i++){
        function a(i){
            aLi[i].onclick = function() {
                alert('You click the ' + i + ' li tag.');
            }
        }

        a(i);
    }
}
bindClick();
```
这时，运行js就能达到我们想要的效果了，函数a就是一个闭包，从上面的代码也可以看到，闭包可以访问当前函数以外的变量。

即使外部函数已经返回，闭包仍能访问外部函数定义的变量
```javascript
function getDate(){
    var date = new Date();
    var year = date.getYear();
    var month = date.getMonth()+1;
    var day = date.getDate();
    var hour = date.getHours();
    var minute = date.getMinutes();
    var second = date.getSeconds();
    function date(){
        console.log('Today is ' + year + '-' + month + '-' + day +'　　' + hour +':' + minute + ':'+ second);
    }
    return date;
}

getDate()();//Today is 2016-5-29　　15:49:35　0.772378174152599
getDate()();//Today is 2016-5-29　　15:49:35　0.1944137749432555
```
闭包可以更新外部的变量
```javascript
function f1(){
    var a = 1;
    function geta() {
        a++;
        alert(a);        
    }
    return geta;    
}

var myFunc = f1();
myFunc();//alert 2
myFunc();//alert 3
```javascript
## 闭包思考题
**思考题一：**
```javascript
var name = "The Window";
var object = {
　　name : "My Object",
　　getNameFunc : function(){
　　　　return function(){
　　　　　　return this.name;
　　　　};
　　}
};
alert(object.getNameFunc()());
```
**思考题二：**
```javascript
var name = "The Window";
var object = {
　　name : "My Object",
　　getNameFunc : function(){
　　　　var that = this;
　　　　return function(){
　　　　　　return that.name;
　　　　};
　　}

};
alert(object.getNameFunc()());
```

作者：xiaoweihuang
链接：https://www.xiaoweihuang.me/2018/11/05/2016-05-29-about-closures/
