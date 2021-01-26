---
title: "gulp移动/复制文件"
categories: [ "JS" ]
tags: [ "gulp" ]
draft: false
slug: "gulp-move-copy-file-1"
date: "2015-04-13 09:08:00"
---

## 有以下文件/文件夹
```
-js/**/*
-css/**/*
-images/**/*
-index.html
```
想要转换成

```bash
-public
    -js/**/*
    -css/**/*
    -images/**/*
    -index.html
```
glob-stream versions >= 3.1.0 (used by gulp >= 3.2.2) accept a base option, which can be used to explicitly set the base.


<!--more-->


只要`gulp`版本 >= `3.2.2`; 可以使用base参数指定拷贝开始目录

```bash
// move
gulp.task('move', function() {
    return gulp.src(
        ['js/**/*jquery*js', 'js/**/*bootstrap*js', 'images/**/*', 'css/**/*bootstrap*css'], {
            base: './'   //如果设置为 base: 'js' 将只会复制 js目录下文件, 其他文件会忽略
        }
    ).pipe(gulp.dest('public'));
});
```
**gulp.src(globs[, options])** 中options用法

存在文件 `app/views/index.html`, `gulp`代码如下

```bash
var src = 'index.html';
var opt = {
  cwd: 'app/views',
  base: 'app'
};
gulp.src(src, opt);
```
*意思为*: `opt.cwd` 路径下读取 `src`, 输出路径的 `base` 为 `opt.base`; 即输出路径为 `views/index.html`;
假设 `opt.base` 为 `app2` , 因为`base`路径基于 `opt.cwd + src`, 所以 输出路径为 空 , 即没有任何文件能输出