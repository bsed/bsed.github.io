---
title: "Node.js中fs.rename出错Error EXDEV"
categories: [ "Java" ]
tags: [ "nodejs" ]
draft: false
slug: "nodejs-fsrename-error-error-exdev"
date: "2014-12-16 08:58:00"
---

## 解决办法

### 方法一
在项目入口的文件（app.js）的顶部， 加以下这行代码
```bash
process.env.TMPDIR = './temp';
// 或者
// process.env.TMPDIR = '/path/to/directory';
```
### 方法二
在项目根目录路径，在命令行下 输入
```bash
env TMPDIR=/path/to/directory node app.js
```


<!--more-->


### 方法三
利用`fs`的`createReadStream`、`createWriteSream`和`unlinkSync`方法
```bash
var fs = require("fs"),
    util = require('util');
    ...
    
var readStream = fs.createReadStream(files.upload.path)
var writeStream = fs.createWriteStream("/tmp/test.png");
util.pump(readStream, writeStream, function() {
    fs.unlinkSync(files.upload.path);
});
```
### 方法四
如果使用 [formidable](https://github.com/felixge/node-formidable)

```bash
var form = new formidable.IncomingForm();
form.uploadDir = dir;// 直接设置路径即可
```