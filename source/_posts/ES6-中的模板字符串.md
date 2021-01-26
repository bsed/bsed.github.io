---
title: "ES6 中的模板字符串"
categories: [ "Java" ]
tags: [  ]
draft: false
slug: "template-string-in-es6"
date: "2015-08-07 09:28:00"
---

### ES6 Template String

```javascript
//Basic usage with an expression placeholder
var person = 'zhang san';
console.log(`Yo! My name is ${person}`);

//Expressions work just as well with object literals
var user = {name: 'li si'};
console.log(`Thanks, ${user.name}`);


<!--more-->


//Expression interpolation, One use is readable inline math.
var a = 50;
var b = 100;
console.log(`The number of JS frameworks is ${a + b} and not ${ 2 * a +b }`);

//Multi-line strings without needing \n\
console.log(`string text line 1
string text line 2`);

//Function inside expressions
function fn() {return "result"}
console.log(`foo ${fn()} bar`);
```
#### 浏览器支持情况

- firefox34 及其以上版本
- chrome 马上会支持

#### 目前解决浏览器兼容方案

- [es6TemplateString][2]: 基于[ejs][0]和[multiline][1]实现

#### 用途

- 国际化
- HTML模板
- 多行文本

#### [ES6 Features][3]

#### [ES6 Features兼容表][4]

#### [ES6 Template String PPT][5]

[0]:https://github.com/tj/ejs
[1]:https://github.com/sindresorhus/multiline
[2]:https://github.com/hjzheng/es6TemplateString
[3]:https://github.com/lukehoban/es6features
[4]:https://kangax.github.io/compat-table/es6/
[5]:http://get-set.cn/CUF_PPTs/ES6_Template_String.html#/