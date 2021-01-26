---
title: "Gulp.js：比 Grunt 更简单的自动化的项目构建利器"
categories: [ "日常","JS" ]
tags: [ "gulp","js" ]
draft: false
slug: "gulp-js"
date: "2014-08-28 21:27:00"
---

> [Gulp](https://github.com/wearefractal/gulp/) 是一款基于任务的设计模式的自动化工具，能给你的开发效率来一次飞跃。
  
在很多场合都会听到前端工和 node 程师推荐 Grunt 来实现项目的自动化，自动化可以自动完成 javascript/coffee/sass/less 等文件的的测试、检查、合并、压缩、格式化、部署文件生成，并监听文件在改动后重复指定的这些步骤。
![a84cbe09a6fc383b43833c60b3f2aa29.png][1]

<!--more-->


得益于 Grunt 基于任务的设计模式，这些步骤可以很好的归类执行，让开发效率得到了一次提升，但杀敌 1000，得自损 800 啊，学习曲线有点高。

Grunt.js 太复杂了，复杂到比使用和配置 Vim 都困难。恰好看到有人推荐 Gulp，打开网站的瞬间就明白：短短 4 段文字就说清安装和使用方法，没错就选 Gulp 了。
Gulp 简明教程：

Gulp 是基于 node.js 的，所以你需要先安装 node.js http://nodejs.org/download/

1. 安装 Gulp

    npm install -g gulp
    npm install —-save-dev gulp

2. 安装插件

    npm install gulp-compass --save-dev

3. 创建配置文件 gulpfile.js

在项目的根目录创建配置文件 gulpfile.js，Gulp 仅有 5 个方法就能组合出你需要的任务流程：task, run, watch, src, dest

来看一个典型的 gulpfile.js 文件，该文件执行 Compass 任务，Compass 包含 SASS 大量定义好的 mixin，函数，以及对 SASS 的扩展：

    // 引入 gulp
    var gulp = require('gulp');
    
    // 引入 Plugins
    var compass = require('gulp-compass');
    
    // 创建 Compass 任务
    gulp.task('compass', function() {
      gulp.src('./scss/**')
        .pipe(compass({
            comments: false,
            css: 'css',
            sass: 'scss',
            image: 'img'
        }));
    });
    
    // 默认任务
    gulp.task('default', function() {
        gulp.run('compass');
    
        gulp.watch([
            './scss/**',
            './img/**'
            ], function(event) {
            gulp.run('compass');
        });
    });

4. 运行 Gulp

在项目目录下执行 `gulp` 命令就会运行 `default` 任务：先运行一遍 `compass` 任务，然后监视 scss 和 img 目录的变动，如果有改动再执行 `compass`任务。

执行 gulp compass 就能运行 compass 任务，非常方便，还有更多高级的配置和使用方法，可以看更详细的官方文档。

示例配置：Gulp 实现完整的 SASS 自动编译并刷新网页
官方网站：http://gulpjs.com/
使用文档：https://github.com/wearefractal/gulp/
插件列表：http://gratimax.github.io/search-gulp-plugins/


  [1]: http://yamlimg01.b0.upaiyun.com/2014/08/268987737.png