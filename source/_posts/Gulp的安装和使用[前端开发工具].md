---
title: "Gulp的安装和使用[前端开发工具]"
categories: [ "Css" ]
tags: [ "gulp","grunt","npm" ]
draft: false
slug: "gulp-installation-and-use-front-end-development-tools"
date: "2015-08-08 10:35:00"
---

> 本文是gulp的入门级介绍，主要内容包括什么是gulp，gulp与grunt有什么区别，gulp可以解决grunt存在的哪些问题，以及一个简单的说明例子。

# gulp的安装
## 什么是gulp
![2112312321.png][1]
gulp的官方定义非常简洁： 基于文件流的构建系统 。这里强调了 streaming，也就是gulp与grunt的在构建流程上的主要区别。具体区别在哪里，后面会简单介绍。

# 另一个grunt？


<!--more-->


相信很多前端的同学对grunt都不陌生，grunt的出现可以说是前端的福音，之前很多需要人肉完成的重复工作，用了grunt，一个命令就搞定了。

说到这里，很多同学可能会比较疑问：既然有了grunt，那同样定位于前端构建的 gulp存在的意义是 ？从gulp的介绍来看，gulp正是为了解决前端同学在使用grunt过程中遇到的这样那样的问题而出现的。是哪些问题呢？在  [http://slid.es/contra/gulp](http://slid.es/contra/gulp)  这个slide里，提到了几点，比如： 

**grunt存在的一些问题**

1.插件职能不够单一

2.插件完成了本不该由插件完成的事情（这个我有点迷糊，为什么说是 things don't need to be plugins？）

3.配置过于复杂

4.由于糟糕的流程控制导致的临时文件/目录

    Plugins do multiple things
        Want a banner? Use the javascript minifier
    Plugins do things that don't need to be plugins

        Need to run your tests? Use a plugin
    Grunt config format is a mess that tries to do everything

        Not idiomatic with "the node way"
    Headache of temp files/folders due to bad flow control

## 用grunt的方式构建

前面列举了四点grunt使用过程中存在的问题，其中1、2点个人觉得略显牵强，插件职能不够单一，或者完成了不该由插件完成的事情，这个跟grunt其实关系并不大，更多的应该归责于插件的作者（当然使用频率最高的那部分插件的作者就是grunt团队的兄弟）。

比较认同的是后面两点： 复杂的配置 、 糟糕的流程控制 。

配置这个是否复杂就不说太多了，在这点上可能争议会比较大。而 糟糕的流程控制 这点是被诟病较多的，尤其是在规模稍大的项目里面。下面这张简图是grunt目前的工作流程：读文件、修改文件、写文件——读文件、修改文件、写文件——。。。

问题显而易见：

1、 效率低下 ：频繁的磁盘IO会使得构建效率变得低下

2、 无法有效串联 ：读文件、修改文件、写文件的循环，导致插件与插件之前的工作无法有效串联起来。 

## 举个例子

比如项目下有个index.html、app.scss、app.js，而index.html里引用了app.css、app.js，如下所示。假设最终的目的是将编译压缩后的app.css、压缩后的app.js 内联到index.html里，同时要保留压缩前的app.css、app.js源文件，那么过程可能如下：（不一定完全准确）

1.将index.html、app.js、编译生成的app.css 拷贝到 dist/ 下

2.压缩 app.js、app.css，并生成到临时目录 .tmp/ 下

3.将 .tmp/app.js、.tmp/app.css 内联到 dist/index.html里

    <html>
    <head>
    <link type="text/css" rel="stylesheet" href="app.css" />
    </head>
    <body>
    
    <script src="app.js"></script>
    
    </body>
    </html>

## 用gulp的方式构建

从上面的构建流程可以看到，多次文件读写以及临时目录就这样以一种难以避免的姿态出现了。在gulp作者的构想里，合理的构建流程应该是这样的：读取文件——修改文件——修改文件。。。——写文件

按照这种设想，上面举的例子用gulp重写，过程应该是这样

1.读文件：读取index.html、app.js、app.css（读文件）

2.编译、压缩app.css，压缩app.js（处理文件流）

3.将A、B内联到index.html中（还是处理文件流）

4.写文件 ：将最终生成地结果写到 dist/ 目录下 （修改后的index.html、编译后的app.css、未修改过的app.js）

# 压缩文件的简单例子

1.首先全局安装gulp命令行工具（相当于grunt-cli）

    npm install -g gulp

2.然后，在项目下安装gulp（相当于grunt）、gulp-uglify

    npm install --save-dev gulp gulp-uglify

3.在项目根目录下创建 gulpfile.js

    var gulp = require('gulp'),
      uglify = require('gulp-uglify');
    
    gulp.task('default', function(){
      gulp.src('src/app.js')
        .pipe(uglify())
        .pipe(gulp.dest('dist/'));
    });

4.运行gulp

## gulp

还看到哪些不同

从上面的例子可以看到，gulp似乎跟grunt有点像（同样是命令行工具与本地构建工具结合），但区别也是很明显的。 grunt的一切基于配置 （配置即任务），而 gulp则是`code based workflow` （其实到最后也是一堆配置，只不过可读性上大大提高）。这里有 [更详细的介绍](http://slid.es/contra/gulp) ，摘要如下。其中个人觉得比较重要的是第二、第五点。第五点已经讲过了。

关于第二点，个人的理解是，我们使用gulp插件时，只需要理解插件本身依赖的那个库的原始配置就可以了，而不是像grunt那样，经常都是将配置包装一层后再暴露给使用者，比如`grunt-contrib-compass`。尽管可能是为了让插件之间的配置更加统一，但的确导致了额外的理解成本。

    With Gulp your build file is code, not config
    You use standard libraries to do things
    Plugins are simple and do one thing - most are a ~20 line function
    Tasks are executed with maximum concurrency
    I/O works the way you picture it

# gulp使用 技巧
## 选择Gulp组件

前端项目需要的功能：
1.图片（压缩图片支持jpg、png、gif）
2.样式 （支持sass 同时支持合并、压缩、重命名）
3.javascript （检查、合并、压缩、重命名）
4.html （压缩）
5.客户端同步刷新显示修改
6.构建项目前清除发布环境下的文件（保持发布环境的清洁）

通过`gulp plugins`，寻找对于的gulp组件
`gulp-imagemin`: 压缩图片
`gulp-ruby-sass`: 支持sass
`gulp-minify-css`: 压缩css
`gulp-jshint`: 检查js
`gulp-uglify`: 压缩js
`gulp-concat`: 合并文件
`gulp-rename`: 重命名文件
`gulp-htmlmin`: 压缩html
`gulp-clean`: 清空文件夹
`gulp-livereload`: 服务器控制客户端同步刷新（需配合chrome插件LiveReload及tiny-lr）

## 安装Gulp组件

安装组件项目目录，通过 `cd project` 进入目录，执行下边的npm安装组件。

    npm install gulp-util gulp-imagemin gulp-ruby-sass gulp-minify-css gulp-jshint gulp-uglify gulp-rename gulp-concat gulp-clean gulp-livereload tiny-lr --save-dev

## 项目目录结构

project(项目名称)

    |–.git 通过git管理项目会生成这个文件夹
    |–node_modules 组件目录
    |–dist 发布环境
        |–css 样式文件(style.css style.min.css)
        |–images 图片文件(压缩图片)
        |–js js文件(main.js main.min.js)
        |–index.html 静态文件(压缩html)
    |–src 生产环境
        |–sass sass文件
        |–images 图片文件
        |–js js文件
        |–index.html 静态文件
    |–.jshintrc jshint配置文件
    |–gulpfile.js gulp任务文件

## gulp基础语法

gulp通过`gulpfile`文件来完成相关任务，因此项目中必须包含gulpfile.js

gulp有五个方法：`src`、`dest`、`task`、`run`、`watch`
**src**和**dest**：指定源文件和处理后文件的路径
**watch**：用来监听文件的变化
**task**：指定任务
**run**：执行任务

## 编写gulp任务

    /**
     * 组件安装
     * npm install gulp-util gulp-imagemin gulp-ruby-sass gulp-minify-css gulp-jshint gulp-uglify gulp-rename gulp-concat gulp-clean gulp-livereload tiny-lr --save-dev
     */
    
    // 引入 gulp及组件
    var gulp    = require('gulp'),                 //基础库
        imagemin = require('gulp-imagemin'),       //图片压缩
        sass = require('gulp-ruby-sass'),          //sass
        minifycss = require('gulp-minify-css'),    //css压缩
        jshint = require('gulp-jshint'),           //js检查
        uglify  = require('gulp-uglify'),          //js压缩
        rename = require('gulp-rename'),           //重命名
        concat  = require('gulp-concat'),          //合并文件
        clean = require('gulp-clean'),             //清空文件夹
        tinylr = require('tiny-lr'),               //livereload
        server = tinylr(),
        port = 35729,
        livereload = require('gulp-livereload');   //livereload
    
    // HTML处理
    gulp.task('html', function() {
        var htmlSrc = './src/*.html',
            htmlDst = './dist/';
    
        gulp.src(htmlSrc)
            .pipe(livereload(server))
            .pipe(gulp.dest(htmlDst))
    });
    
    // 样式处理
    gulp.task('css', function () {
        var cssSrc = './src/scss/*.scss',
            cssDst = './dist/css';
    
        gulp.src(cssSrc)
            .pipe(sass({ style: 'expanded'}))
            .pipe(gulp.dest(cssDst))
            .pipe(rename({ suffix: '.min' }))
            .pipe(minifycss())
            .pipe(livereload(server))
            .pipe(gulp.dest(cssDst));
    });
    
    // 图片处理
    gulp.task('images', function(){
        var imgSrc = './src/images/**/*',
            imgDst = './dist/images';
        gulp.src(imgSrc)
            .pipe(imagemin())
            .pipe(livereload(server))
            .pipe(gulp.dest(imgDst));
    })
    
    // js处理
    gulp.task('js', function () {
        var jsSrc = './src/js/*.js',
            jsDst ='./dist/js';
    
        gulp.src(jsSrc)
            .pipe(jshint('.jshintrc'))
            .pipe(jshint.reporter('default'))
            .pipe(concat('main.js'))
            .pipe(gulp.dest(jsDst))
            .pipe(rename({ suffix: '.min' }))
            .pipe(uglify())
            .pipe(livereload(server))
            .pipe(gulp.dest(jsDst));
    });
    
    // 清空图片、样式、js
    gulp.task('clean', function() {
        gulp.src(['./dist/css', './dist/js', './dist/images'], {read: false})
            .pipe(clean());
    });
    
    // 默认任务 清空图片、样式、js并重建 运行语句 gulp
    gulp.task('default', ['clean'], function(){
        gulp.start('html','css','images','js');
    });
    
    // 监听任务 运行语句 gulp watch
    gulp.task('watch',function(){
    
        server.listen(port, function(err){
            if (err) {
                return console.log(err);
            }
    
            // 监听html
            gulp.watch('./src/*.html', function(event){
                gulp.run('html');
            })
    
            // 监听css
            gulp.watch('./src/scss/*.scss', function(){
                gulp.run('css');
            });
    
            // 监听images
            gulp.watch('./src/images/**/*', function(){
                gulp.run('images');
            });
    
            // 监听js
            gulp.watch('./src/js/*.js', function(){
                gulp.run('js');
            });
    
        });
    });

## LiveReload配置

1.安装Chrome [LiveReload](https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei)
2.通过npm安装[http-server](https://www.npmjs.org/package/http-server) ，快速建立http服务 `npm install http-server -g`
3.通过cd找到发布环境目录dist
4.运行http-server，默认端口是8080
5.访问路径localhost:8080
6.再打开一个cmd，通过cd找到项目路径执行gulp，清空发布环境并初始化
7.执行监控 gulp
8.点击chrome上的LiveReload插件，空心变成实心即关联上，你可以修改css、js、html即时会显示到页面中。

视频教材youtube（需要翻墙）：http://www.youtube.com/watch?v=OKVE6wE9CW4

## 项目git

配置好的项目已经放到github上。
下载地址：[https://github.com/dbpoo/gulp](https://github.com/dbpoo/gulp)
git clone地址：`git@github.com:dbpoo/gulp.git`

# 一些链接：

gulp的github地址： [https://github.com/gulpjs/gulp](https://github.com/gulpjs/gulp)

gulp，The streaming build system： [http://slid.es/contra/gulp](http://slid.es/contra/gulp)

FIS官网： [http://fis.baidu.com/](http://fis.baidu.com/) 

Gulp中文网: [http://www.gulpjs.com.cn/docs/recipes/](http://www.gulpjs.com.cn/docs/recipes/)

Gulp详细入门教程: [http://www.dtao.org/archives/18](http://www.dtao.org/archives/18)

Gulp中文API: [http://www.dtao.org/archives/424](http://www.dtao.org/archives/424)

Gulp  LESS 的使用：[http://www.dtao.org/archives/34](http://www.dtao.org/archives/34)

**如何快速切换NPM源**: [快速切换 NPM 源](https://yigrherb.com/2014/05/08/nrm-fast-switching-npm-source.html)


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/3146030435.png