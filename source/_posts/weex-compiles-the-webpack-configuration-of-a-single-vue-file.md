---
title: Weex 编译单个 Vue 文件的 Webpack 配置
date: 2017-09-08 13:23:00
updated: 2017-09-08 13:20:45
tags: 
- git
categories: 
- linux

---
一个诡异的现象, 使用以下配置的 webpack 打包的 weex 文件，在 Android 里一直转菊花，而且 Android Monitor 里没有有效的报错信息

```
var testConfig =  {
    entry: {
      // 'test_b': path.resolve('src/views', 'test_b.vue?entry=true'),
      'test_b': path.resolve('src/views', 'test_b.vue'),
    },
    output: {
      path: path.resolve(__dirname, 'dist'),
        filename: 'test_b.weex.js'
    },
    module: {
      rules: [
        {
          test: /\.js$/,
          loader: 'babel-loader',
          exclude: /node_modules/
        }, {
          test: /\.vue(\?[^?]+)?$/,
          loaders: ['weex-loader']
        }
      ]
    },
    plugins: [
      // new webpack.optimize.UglifyJsPlugin({compress: { warnings: false }}),
      bannerPlugin
    ]
}

```


<!--more-->


对比了 Github 上 Weex 的 Apache 项目的 webpack 配置，发现我少了 entry=true 的配置。 在 VUE 文件名后面缀上 entry=true 之后，编译之后的文件就能够正常运行了。

这是为什么呢？

使用 vimdiff 对比加了 entry=true 前后的 bundle 文件，发现文件增加了两行

```
module.exports.el = 'true'    
new Vue(module.exports) 

```

查看 weex-loader 的源码中的 loader.js 文件，发现的确会根据 entry 的值进行不同的操作。
