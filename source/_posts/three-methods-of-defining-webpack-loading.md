---
title: 定义Webpack加载的三种方法
date: 2016-10-23 16:53:00
updated: 2016-10-24 21:44:32
tags: 
- javascript
- jstips
categories: 
- js

---
> `Webpack loaders` 执行相反的多少操作(从右到左的字符串时,基层当数组),最后[加载器](https://webpack.github.io/docs/loaders.html#loader-order)返回的Javascript。

一旦过去,接下来的实现细节处理写查询参数配置每个加载程序,显得很笨拙。

## 写在一行的查询字符串

```javascript
module.exports = {
  module: {
    loaders: [
      {
        test: /\.scss$/,
        loader: 'style!css!autoprefixer?browsers=last 2 version!sass?outputStyle=expanded',
      },
    ],
  },
}
```


<!--more-->


## 查询字符串数组
```javascript
module.exports = {
  module: {
    loaders: [
      {
        test: /\.scss$/,
        loaders: [
          'style',
          'css',
          'autoprefixer?browsers=last 2 version',
          'sass?outputStyle=expanded',
        ],
      },
    ],
  },
}
```
## 细化的查询字符串数组：

```javascript
module.exports = {
  module: {
    loaders: [
      {
        test: /\.scss$/,
        loader: 'style',
      },
      {
        test: /\.scss$/,
        loader: 'css',
      },
      {
        test: /\.scss$/,
        loader: 'autoprefixer',
        query: { browsers: 'last 2 version' },
      },
      {
        test: /\.scss$/,
        loader: 'sass',
        query: { outputStyle: 'expanded' },
      },
    ],
  },
}
```

就我个人而言,我更喜欢最后的版本,因为它更容易构成一连串的压缩器和加载器配置复杂的查询。

