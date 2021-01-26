---
title: "如何用gulp移动/复制文件"
categories: [ "JS" ]
tags: [ "gulp","move","task" ]
draft: false
slug: "gulp-move-copy-file"
date: "2014-08-11 16:27:00"
---

有以下文件/文件夹

    -js/**/*
    -css/**/*
    -images/**/*
    -index.html


<!--more-->


想要转换成

    -public
        -js/**/*
        -css/**/*
        -images/**/*
        -index.html

glob-stream versions >= 3.1.0 (used by gulp >= 3.2.2) accept a base option, which can be used to explicitly set the base.

只要gulp版本 >= 3.2.2; 可以使用base参数指定拷贝开始目录

    // move
    gulp.task('move', function() {
        return gulp.src(
            ['js/**/*jquery*js', 'js/**/*bootstrap*js', 'images/**/*', 'css/**/*bootstrap*css'], {
                base: './'   //如果设置为 base: 'js' 将只会复制 js目录下文件, 其他文件会忽略
            }
        ).pipe(gulp.dest('public'));
    });

参考资料:

[http://www.levihackwith.com/how-to-make-gulp-copy-a-directory-and-its-contents/](http://www.levihackwith.com/how-to-make-gulp-copy-a-directory-and-its-contents/)
[https://github.com/hparra/gulp-rename](https://github.com/hparra/gulp-rename)


