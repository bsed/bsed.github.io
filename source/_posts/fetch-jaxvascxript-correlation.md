---
title: JavaScript Fetch 相关学习
date: 2016-11-12 10:32:00
updated: 2016-11-12 10:32:41
tags: 
- ssh
- bash
categories: 
- java

---
## Webpack 项目中使用fetch.js

### 安装依赖

```bash
npm i imports-loader exports-loader --save-dev  
npm i whatwg-fetch --save  
```
方法一: webpack配置


<!--more-->


### 修改webpack.config.js 文件
```bash
 plugins: [
        new webpack.ProvidePlugin({
            'fetch': 'imports?this=>global!exports?global.fetch!whatwg-fetch'
        }),
    ],
```
## 方法二: import

在需要使用的文件中import
```bash
import 'imports?this=>global!exports?global.fetch!whatwg-fetch';  
```
相关阅读（必读）：

 - [JavaScript Fetch API](http://www.tuicool.com/articles/QZBJ7zJ)
 - [传统 Ajax 已死，Fetch
   永生](https://segmentfault.com/a/1190000003810652?utm_source=tuicool&utm_medium=referral)