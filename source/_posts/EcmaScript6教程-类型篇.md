---
title: "EcmaScript6教程 类型篇"
categories: [ "JS" ]
tags: [ "javascript","ecmascript6" ]
draft: false
slug: "ecmascript6-tutorial-type"
date: "2015-11-01 10:02:00"
---

## object

### 按照字面语法创建对象

    // bad
    var item = new Object();
    
    // good
    var item = {};

### 不要使用关键字或保留字，在IE8中没用

    // bad
    var superman = {
      default: { clark: 'kent' },
      private: true
    };
    
    // good
    var superman = {
      defaults: { clark: 'kent' },
      hidden: true
    };


<!--more-->


### 避免关键字，使用同义词

    // bad
    var superman = {
      class: 'alien'
    };
    
    // bad
    var superman = {
      klass: 'alien'
    };
    
    // good
    var superman = {
      type: 'alien'
    };

## Array

### 使用字面语法创建

    // bad
    var items = new Array();
    
    // good
    var items = [];

### 使用Array.push代替直接赋值

    var someStack = [];
    
    
    // bad
    someStack[someStack.length] = 'abracadabra';
    
    // good
    someStack.push('abracadabra');

### 当你需要拷贝数组的时候

    var len = items.length;
    var itemsCopy = [];
    var i;
    
    // bad
    for (i = 0; i < len; i++) {
      itemsCopy[i] = items[i];
    }
    
    // good
    itemsCopy = items.slice();

### 把类数组对象转成数组

    function trigger() {
      var args = Array.prototype.slice.call(arguments);
      ...
    }

## string

### 在字符串上使用单引号

    // bad
    var name = "Bob Parr";
    
    // good
    var name = 'Bob Parr';
    
    // bad
    var fullName = "Bob " + this.lastName;
    
    // good
    var fullName = 'Bob ' + this.lastName;

### 如果你的一行超过80个字符的话，那你应该考虑给它换行了
如果真的超过了那么长

    // bad
    var errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';
    
    // bad
    var errorMessage = 'This is a super long error that was thrown because \
    of Batman. When you stop to think about how Batman had anything to do \
    with this, you would get nowhere \
    fast.';
    
    // good
    var errorMessage = 'This is a super long error that was thrown because ' +
      'of Batman. When you stop to think about how Batman had anything to do ' +
      'with this, you would get nowhere fast.';

### 当你尝试做字符串拼接的时候，尽量使用Array.join来做

    var items;
    var messages;
    var length;
    var i;
    
    messages = [{
      state: 'success',
      message: 'This one worked.'
    }, {
      state: 'success',
      message: 'This one worked as well.'
    }, {
      state: 'error',
      message: 'This one did not work.'
    }];
    
    length = messages.length;
    
    // bad
    function inbox(messages) {
      items = '<ul>';
    
      for (i = 0; i < length; i++) {
        items += '<li>' + messages[i].message + '</li>';
      }
    
      return items + '</ul>';
    }
    
    // good
    function inbox(messages) {
      items = [];
    
      for (i = 0; i < length; i++) {
        // use direct assignment in this case because we're micro-optimizing.
        items[i] = '<li>' + messages[i].message + '</li>';
      }
    
      return '<ul>' + items.join('') + '</ul>';
    }

## Function

### function 表达式

    // 匿名函数表达式
    var anonymous = function() {
      return true;
    };
    
    // 命名函数表达式
    var named = function named() {
      return true;
    };
    
    // 立即执行函数表达式
    (function() {
      console.log('Welcome to the Internet. Please follow me.');
    })();

### 不要在一个非函数块（如if, while等）中声明一个函数，将声明函数复制给变量。

ECMA-262将一系列表达式定义为块，而函数声明不是一个块。

    // bad
    if (currentUser) {
      function test() {
        console.log('Nope.');
      }
    }
    
    // good
    var test;
    if (currentUser) {
      test = function test() {
        console.log('Yup.');
      };
    }

不要给一个参数起名为arguments,它会覆盖原有的 arguments 对象的

    // bad
    function nope(name, options, arguments) {
      // ...stuff...
    }
    
    // good
    function yup(name, options, args) {
      // ...stuff...
    }






