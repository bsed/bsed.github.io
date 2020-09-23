---
title: Webpack 项目中使用fetch.js
date: 2015-08-24 16:11:00
updated: 2017-02-12 16:12:57
tags: 
- swift
categories: 
- ios

---
## 安装依赖
```bash
npm i imports-loader exports-loader --save-dev  
npm i whatwg-fetch --save  
```
### 方法一: webpack配置


<!--more-->


修改`webpack.config.js` 文件
```json
 plugins: [
        new webpack.ProvidePlugin({
            'fetch': 'imports?this=>global!exports?global.fetch!whatwg-fetch'
        }),
    ],
```
### 方法二: import

在需要使用的文件中import
```json
import 'imports?this=>global!exports?global.fetch!whatwg-fetch';  
```