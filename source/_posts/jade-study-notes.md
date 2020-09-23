---
title: jade学习笔记
date: 2016-12-18 21:49:00
updated: 2016-12-18 21:50:16
tags: 
- javascript
- webpack
categories: 
- js

---
## 什么是jade?

jade是一款源于Node.js的HTML模板引擎。

## 模板引擎
依赖于JavaScript实现jade到HTML的转换
供Node使用
## 安装和编译

### 安装
```bash
npm install -g jade
```


<!--more-->


### 编译
```bash
jade demo.jade
```
#编译后将在同级目录下生成demo.html
### 工具

```
doctype html
```
编译后：
```html
<!DOCTYPE html>
```
注意： !!! 这种简写方式已经废除。
可选值还有：
```
xml
transitional
strict
frameset
1.1
basic
mobile
```
基本结构
```
//jade
doctype html
html
    head
        meta(charset='utf-8')
        title JadeNotes
    body
        h1 Hello Jade!
```
编译后：
```html
<!--html-->
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>JadeNotes</title>
    </head>
<body>
    <h1>Hello Jade!</h1>
</body>
</html>
```
### 注释
```
//这行注释将被编译
//- 这行注释不会被编译
//
    多行注释
    多行注释
    多行注释
```
编译后：
```
<!--这行注释将被编译-->
<!--多行注释
    多行注释
    多行注释-->
```
### 文本
```
p Hello Jade!
```
编译后：
```html
<p>Hello Jade!</p>
```
选择器
```
//类选择器
p.red-txt
//ID选择器
p#blue-txt
//一个ID多个Class
p#yellow-text.high-txt.demo-txt
```
编译后：
```
<!--类选择器-->
<p class="red-txt"></p>
<!--ID选择器-->
<p id="blue-txt"></p>
<!--一个ID多个Class-->
<p id="yellow-text" class="high-txt demo-txt"></p>
```
### 属性
```
input(type='text', placeholder='Input your Phone')
```
编译后：
```html
<input type="text" placeholder="Input your Phone">
```
### 嵌套
```
ul#demo-ul
    li.demo-li Hello
    li.demo-li World
```
编译后：
```html
<ul id="demo-ul">
    <li class="demo-li">Hello</li>
    <li class="demo-li">World</li>
</ul>
```
### 变量
```
- var welcome = 'Hello World'
p 'hello, #{welcome}'
```
编译后：
```
<p>'hello, Hello World'</p>
```
标签中有大段的块内容
```
script.
    console.log('Hello');
    console.log('World');
//或者
script.
    | console.log('Hello');
    | console.log('World');
```
编译后：
```javascript
<script>
console.log('Hello');
console.log('World');
</script>
<!--或者-->
<script>
console.log('Hello')
console.log('World')
</script>
```
### 转义
```
- var welcome = 'Hello <b>Wrold</b>'
p welcome
p= welcome
p!= welcome
```
编译后：
```html
<p>welcome</p>
<p>Hello &lt;b&gt;Wrold&lt;/b&gt;</p>
<p>Hello <b>Wrold</b></p>
```
`=` 会转义内容，不想被转义可以加 `!=` 。

## 循环

使用each循环：
```
- var arr = ['A', 'B', 'C', 'D', 'E']
ul
    each a in arr
        li= a
- var obj = {'name': '老王', 'age': '33', 'money'}
table
    each value, key in obj
        tr
            td #{key}
            td #{value}
编译后：
```html
<ul>
    <li>A</li>
    <li>B</li>
    <li>C</li>
    <li>D</li>
    <li>E</li>
</ul>
<table>
    <tr>
        <td>name</td>
        <td>老王</td>
    </tr>
    <tr>
        <td>age</td>
        <td>33</td>
    </tr>
    <tr>
        <td>money</td>
        <td>1000</td>
    </tr>
</table>
```
### case

case 和JavaScript里的 switch 作用一样。
```
- var money = 1000
case money
    when 10000
        h1 土豪
    when 1000
        h1 地主
    when 100
        h1 平民
    when 1
        h1 穷逼
```
编译后：
```
<h1>地主</h1>
或者：

- var money = 10000000
case money
    when 10000: h1 土豪
    when 1000: h1 地主
    when 100: h1 平民
    when 1: h1 穷逼
    default: h1 你有多少钱？
```
编译后：
```html
<h1>你有多少钱？</h1>
```
### 过滤器

支持markdown。必须是安装了 markdown-js 或者 node-discount 。
```bash
#安装markdown-js
npm install -g markdown-js
#或者安装node-discount
npm install -g node-discount
```
例如：
```
:markdown
    ### Hello Markdown!
```
编译后：
```
<h3>Hello Markdown!</h3>
```
## mixin

无参数的mixin：
```
mixin welcome
    ul
        li 为系统而生，为框架而死，为debug奋斗一辈子 
        li 吃符号的亏，上大小写的当，最后死在需求上！
        li Hello World!
+welcome()
```
编译后：
```
<ul>
    <li>为系统而生，为框架而死，为debug奋斗一辈子</li>
    <li>吃符号的亏，上大小写的当，最后死在需求上！</li>
    <li>Hello World!</li>
</ul>
```
有参数的mixin：
```
mixin welcome(param)
    ul
        li 为系统而生，为框架而死，为debug奋斗一辈子 
        li 吃符号的亏，上大小写的当，最后死在需求上！
        li Hello #{param}!
+welcome('Mixin')
```
编译后：
```
<ul>
    <li>为系统而生，为框架而死，为debug奋斗一辈子</li>
    <li>吃符号的亏，上大小写的当，最后死在需求上！</li>
    <li>Hello Mixin!</li>
</ul>
```
mixin中文支持block：
```
mixin welcome(param)
    ul
        if block
            block
        else
            li Nothing!
        li Hello Wrold!
+welcome()
    li 为系统而生，为框架而死，为debug奋斗一辈子 
    li 吃符号的亏，上大小写的当，最后死在需求上！
```
编译后：
```html
<ul>
    <li>Hello Wrold!</li>
    <li>为系统而生，为框架而死，为debug奋斗一辈子</li>
    <li>吃符号的亏，上大小写的当，最后死在需求上！</li>
</ul>
```
### mixin支持attributes
```
mixin link(href, name)
    a(class != attributes.class, title != attributes.title, href=href)= name
+link('kelvin.mbioq.com', 'xiguaaxigua')(class='main-link', title='kelvin.mbioq.com！')
```
编译后：
```html
<a title="Hello kelvin.mbioq.com！" href="kelvin.mbioq.com" class="main-link">kelvin.mbioq.com</a>
```
## 包含

有点类似freemaker，通过include来载入一些jade模板。

