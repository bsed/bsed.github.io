---
title: Vue2 dist 目录下各个文件的区别 [转]
date: 2019-11-11 10:34:00
updated: 2019-11-11 10:38:05
tags: 
- windows
- psping
categories: 
- default

---
vue2 经过 2.2 版本升级后, 文件变成了 8 个:

vue.common.js
vue.esm.js
vue.js
vue.min.js
vue.runtime.common.js
vue.runtime.esm.js
vue.runtime.js
vue.runtime.min.js

瞬间就懵逼了, 这些文件该怎么选?
下面就来说下, 这 8 个作用都用在什么场景, 有什么区别

按照构建方式分, 可以分成 完整构建(包含独立构建和运行时构建) 和 运行时构建
按照规范分, 可以分成 *UMD*, *CommonJS* 和 *ES Module*

简单来说, 完整构建 和 运行时构建的区别就是, 可不可以用template选项, 和文件大一点,小一点


<!--more-->


## vue.common.js
属于: 基于 CommonJS 的完整构建
可以用于 Webpack-1 和 Browserify 之类打包工具
因为是完整构建, 所以可以使用template选项, 如:
```javascript
import Vue from 'vue'
new Vue({
  template: `
    <div id="app">
      <h1>Basic</h1>
    </div>
  `
}).$mount('#app')
```
注意: 用 webpack-1 之类打包工具时, 使用该版本, 需要配置别名, 以 webpack 为例:
```
{
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.common.js'
    }
  }
}
```
## vue.esm.js
属于: 基于 ES Module 的完整构建
可以用于 Webpack-2 和 rollup 之类打包工具
因为是完整构建, 所以可以使用template选项, 如:
```javascript
import Vue from 'vue'
new Vue({
  template: `
    <div id="app">
      <h1>Basic</h1>
    </div>
  `
}).$mount('#app')
```
注意: 用 webpack-2 之类打包工具时, 使用该版本, 需要配置别名, 以 webpack 为例:
```
{
  resolve: {
    alias: {
      'vue$': 'vue.esm.js'
    }
  }
}
```
## vue.js
属于: 基于 UMD 的完整构建
可以用于直接 CDN 引用
因为是完整构建, 所以可以使用template选项, 如:

<script src="https://unkpg.com/vue/dist/vue.js"></script>
<script>
new Vue({
  template: `
    <div id="app">
      <h1>Hi Vue</h1>
    </div>
  `
}).$mount('#app')
</script>
## vue.min.js
和 vue.js 一样, 属于压缩后版本

## vue.runtime.common.js
属于: 基于 CommonJS 的运行时构建
可以用于 Webpack-1 和 Browserify 之类打包工具
运行时构建不包含模板编译器，因此不支持template选项，只能用render选项，但即使使用运行时构建，在单文件组件中也依然可以写模板，因为单文件组件的模板会在构建时预编译为render函数, render函数的使用, 请参考: [http://cn.vuejs.org/v2/guide/render-function.html](http://cn.vuejs.org/v2/guide/render-function.html)
```javascript
import Vue from 'vue'
new Vue({
  render: function(h){
    return h('h1', 'Hi Vue')
  }
}).$mount('#app')
```
## vue.runtime.esm.js
属于: 基于 ES Module 的运行时构建
可以用于 Webpack-2 和 rollup 之类打包工具
运行时构建不包含模板编译器，因此不支持template选项，只能用render选项，但即使使用运行时构建，在单文件组件中也依然可以写模板，因为单文件组件的模板会在构建时预编译为render函数, render函数的使用, 请参考: [http://cn.vuejs.org/v2/guide/render-function.html](http://cn.vuejs.org/v2/guide/render-function.html)
```
import Vue from 'vue'
new Vue({
  render: function(h){
    return h('h1', 'Hi Vue')
  }
}).$mount('#app')
```
## vue.runtime.js
属于: 基于 UMD 的运行时构建
可以用于直接 CDN 引用
该版本和vue.js类似, 可以用于直接 CDN 引用, 因为不包含编译器, 所以不能使用template选项, 只能使用render函数
```javascript
<script src="https://unkpg.com/vue/dist/vue.runtime.js"></script>
<script>
new Vue({
  render: function(h){
    return h('h1', 'Hi Vue')
  }
}).$mount('#app')
</script>
```
## vue.runtime.min.js
和 vue.runtime.js 一样, 属于压缩后版本

- 原文：[https://www.mmxiaowu.com/article/58482558d4352863efb55475](https://www.mmxiaowu.com/article/58482558d4352863efb55475)