---
title: 如何理解JavaScript 箭头函数新特性 [JS基础增强]
date: 2019-03-25 07:27:00
updated: 2019-09-17 13:57:50
tags: 
- prototype
- 原型链
- JS基础增强
categories: 
- js

---
`箭头函数`（Arrow functions）,是ECMAScript2015中新加的特性，它的产生，主要有以下两个原因：一是使得函数表达式（匿名函数）有更简洁的语法，二是它拥有词法作用域的this值，也就是说它跟父作用域共享this，不会新产生自己作用域下的this, arguments, super 和 new,target 等对象。

## 使用箭头函数特性
在JavaScript代码中，函数无处不在。假设页面上有一个特定的按钮，它的id是‘clickMe’,点击它后，页面弹出“Hello，Arrow functions!”,为实现这个效果，我们会像下面这样编写JavaScript代码：
```javascript
$(function(){
    $('#clickMe').click(function(){
            alert('Hello，Arrow functions!');
    })
})
```


<!--more-->


以上是传统的JavaScript写法，给click方法传入一个函数作为参数，这个函数通常都需要按照以下格式输入：function(){}。使用箭头函数后，代码如下：
```javascript
$(function(){
    $('#clickMe').click(()=>{alert('Hello，Arrow functions!')});
})
```
上面的例子比较简单，再来看一个Promise链的例子：
```javascript
function getUserInfo(id){
    return getUsers(id).
            then(function(users){return users[0]}).
            then(checkUser).
            then(function(user,userInfo){return userInfo }).
            catch(function(error){console.log(error.message)})
}
```
使用箭头函数简化上面的例子，代码如下：
```javascript
function getUserInfo(id){
    return getUsers(id).
        then(users => users[0]).
        then(checkUser).
        then((user,userInfo)=>userInfo).
        catch(error=>console.log(error.message))
}
```
从简化后的代码来看，不难发现，所有回调函数中的function 和 {}不见了，而且回调函数都在一行表示，当只有一个参数时，（）也消失了；由于{}消失了，里面的return也消失了，代码看起来更加清晰、简洁了。前面提到单行表示回调函数的时候，{}被省略了，假如这时候我们要返回一个对象（包括空对象）的话，该怎么处理呢？这是个坑，一般按如下方式书写代码：
```javascript
const emptyObject = ()=>({})
```
## 箭头函数里面的this

开头那里已经提到了，箭头函数没有自己的this值，它跟父作用域共享this,箭头函数内部也没有constructor方法，也没有prototype,所以箭头函数不支持new操作。

在箭头函数之前，每个新定义的函数都有自己的this值，例如，构造函数的this指向一个新的对象，如果是‘严格模式’，则this值为undefined,如果函数作为对象的方法被调用，则该函数的this指向了那个调用它的对象。在JavaScript面向对象编程中，this的指向是让新手很头疼的问题。
```javascript
function Person()｛
    //构造函数的this指向实例对象自己
    this.age = 25;
    setInterval(function growUp(){
        //在非严格模式下，growUp函数定义了其内部的this,其指向window对象，不同于构造函数Person()定义的this
        this.age++;
    },1000);
}
var p = new Person();
```
在箭头函数之前，我们如何使growUp函数内部的this也指向构造函数Person（）的实例对象的呢？如下：
```javascript
function Person() {
    var self = this;
    self.age = 25;
    setInterval(function growUp(){
        self.age++;
    });
}
var p = new Person();
```
也就是通过新增一个变量来指向期望的this对象，除此之外，还可以使用 bind 函数，把期望的 this 值传递给 growUp() 函数。
```javascript
function Person() {
    this.age = 25;
    setInterval(function growUp(){
        this.age++;
        console.log(this.age);
    }.bind(this),1000);
}
var p = new Person();
```
由于箭头函数会捕获其所在上下文的this值，来作为自己的this值，所以我们可以这样修改上述例子的代码：
```javascript
function Person() {
    this.age = 25;
    setInterval(()=>{
        this.age++;//这里的this也指向Person对象
    },1000);
}
```
## 使用 call 或 apply 调用
箭头函数的 this 始终指向函数定义时的 this，而非执行时。下面看一个试图改变箭头函数 this 指针的例子：
```javascript
var x = 1,
o = {
    x : 10,
    test : () => this.x
};

o.test(); // 1
o.test.call(o); // 仍旧是1
```
由于 this 已经在词法层面完成了绑定，通过 call() 或 apply() 方法调用一个函数时，只是传入了参数而已，对 this 并没有什么影响：
```javascript
var adder = {
  base : 1,

  add : function(a) {
    var f = v => v + this.base;
    return f(a);
  },

  addThruCall: function(a) {
    var f = v => v + this.base;
    var b = {
      base : 2
    };

    return f.call(b, a);
  }
};

console.log(adder.add(1));         // 输出 2
console.log(adder.addThruCall(1)); // 仍然输出 2，而不是3
```

作者：xiaoweihuang
链接：https://www.xiaoweihuang.me/2018/11/05/2016-08-29-arrow-functions/
