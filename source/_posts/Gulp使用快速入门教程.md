---
title: "Gulp使用快速入门教程"
categories: [ "JS" ]
tags: [ "gulp","nodejs" ]
draft: false
slug: "gulp-using-quick-start-tutorial"
date: "2014-10-20 15:54:00"
---

## 前言

最近流行前端构建工具，苦于之前使用Grunt，代码很难阅读，现在出了Gulp，真是摆脱了痛苦。发现了一篇很好的Gulp英文教程，整理翻译给大家看看。

## 为什么使用Gulp

Gulp基于Node.js的前端构建工具，通过Gulp的插件可以实现前端代码的编译（sass、less）、压缩、测试；图片的压缩；浏览器自动刷新，还有许多强大的插件可以在这里查找。比起Grunt不仅配置简单而且更容易阅读和维护，我们可以做一个对比：
*Grunt:*


<!--more-->


    sass: {
      dist: {
        options: {
          style: 'expanded'
        },
        files: {
          'dist/assets/css/main.css': 'src/styles/main.scss',
        }
      }
    },
    autoprefixer: {
      dist: {
        options: {
          browsers: [
            'last 2 version', 'safari 5', 'ie 8', 'ie 9', 'opera 12.1', 'ios 6', 'android 4'
          ]
        },
        src: 'dist/assets/css/main.css',
        dest: 'dist/assets/css/main.css'
      }
    },
    grunt.registerTask('styles', ['sass', 'autoprefixer']);

在Grunt里面，每个插件使用的方式相对独立，正如上面的代码通过sass插件将main.sass文件编译成main.css文件，接着autoprefixer插件再对编译好的main.css文件进行修改，最后覆盖main.css。那么覆盖文件就是多余的了，有没办法做到sass和autoprefixer一并处理完再生成main.css？我们来看看Gulp是如何做到的：

*Gulp：*

    gulp.task('sass', function() {
      return gulp.src('src/styles/main.scss')
        .pipe(sass({ style: 'compressed' }))
        .pipe(autoprefixer('last 2 version', 'safari 5', 'ie 8', 'ie 9', 'opera 12.1', 'ios 6', 'android 4'))
        .pipe(gulp.dest('dist/assets/css'))
    });
使用Gulp我们只需要放一个路径，通过管道方式使用插件，最后生成文件，是不是有种jQuery的感觉。这种方式不仅提高效率而且一眼就看清了输入文件和输出文件，再也不用看gruntfile看得眼花缭乱了。

再打个比喻，Grunt的插件就像独立的工厂，这个工厂生成出来的产品打包封装好后再送到另一个工厂去加工，使用了Gulp后实现了工厂的合并，所有东西都在一个工厂里完成了。现在是否对Gulp感兴趣了，那就开始使用Gulp吧！
安装

首先我们要全局安装一遍：

    $ npm install gulp -g

接着我们要进去到项目的根目录再安装一遍（确保你根目录存在package.json文件）：

    $ npm install gulp --save-dev

—save-dev这个属性会将条目保存到你package.json的devDependencies里面
安装Gulp插件

我们将要使用Gulp插件来完成我们以下任务：

    sass的编译（gulp-ruby-sass）
    自动添加css前缀（gulp-autoprefixer）
    压缩css（gulp-minify-css）
    js代码校验（gulp-jshint）
    合并js文件（gulp-concat）
    压缩js代码（gulp-uglify）
    压缩图片（gulp-imagemin）
    自动刷新页面（gulp-livereload）
    图片缓存，只有图片替换了才压缩（gulp-cache）
    更改提醒（gulp-notify）
    清除文件（del）

安装这些插件需要运行如下命令：

    $ npm install gulp-ruby-sass gulp-autoprefixer gulp-minify-css gulp-jshint gulp-concat gulp-uglify gulp-imagemin gulp-notify gulp-rename gulp-livereload gulp-cache del --save-dev

上面是一些常用的插件，如果想要找更多的插件点击[这里](https://gratimax.net/search-gulp-plugins/)
加载插件

接着我们要创建一个gulpfile.js在根目录下，然后在里面加载插件：

    var gulp = require('gulp'),
        sass = require('gulp-ruby-sass'),
        autoprefixer = require('gulp-autoprefixer'),
        minifycss = require('gulp-minify-css'),
        jshint = require('gulp-jshint'),
        uglify = require('gulp-uglify'),
        imagemin = require('gulp-imagemin'),
        rename = require('gulp-rename'),
        concat = require('gulp-concat'),
        notify = require('gulp-notify'),
        cache = require('gulp-cache'),
        livereload = require('gulp-livereload'),
        del = require('del');

Gulp的插件和Grunt有些许不一样，Grunt插件是为了更好的完成一项任务。就像Grunt的`imagemin`插件就利用了缓存来避免重复压缩，而Gulp要利用`gulp-cache`来完成，当然啦，不仅限定于缓存图片。

## 建立任务

### 编译sass、自动添加css前缀和压缩

首先我们编译sass，添加前缀，保存到我们指定的目录下面，还没结束，我们还要压缩，给文件添加.min后缀再输出压缩文件到指定目录，最后提醒任务完成了：

    gulp.task('styles', function() {
      return gulp.src('src/styles/main.scss')
        .pipe(sass({ style: 'expanded' }))
        .pipe(autoprefixer('last 2 version', 'safari 5', 'ie 8', 'ie 9', 'opera 12.1', 'ios 6', 'android 4'))
        .pipe(gulp.dest('dist/assets/css'))
        .pipe(rename({suffix: '.min'}))
        .pipe(minifycss())
        .pipe(gulp.dest('dist/assets/css'))
        .pipe(notify({ message: 'Styles task complete' }));
    });

让我解释一下：

    gulp.task('styles', function () {...});

gulp.task这个API用来创建任务，在命令行下可以输入$ gulp styles来执行上面的任务。

    return gulp.src('src/styles/main.scss')

gulp.src这个API设置需要处理的文件的路径，可以是多个文件以数组的形式[main.scss, vender.scss]，也可以是正则表达式/**/*.scss。

    .pipe(sass({ style: 'expanded' }))

我们使用`.pipe()`这个API将需要处理的文件导向sass插件，那些插件的用法可以在github上找到，为了方便大家查找我已经在上面列出来了。

    .pipe(gulp.dest('dist/assets/css'));

gulp.dest()API设置生成文件的路径，一个任务可以有多个生成路径，一个可以输出未压缩的版本，另一个可以输出压缩后的版本。

为了更好的了解Gulp API，强烈建议看一下 Gulp API](https://github.com/gulpjs/gulp/blob/master/docs/API.md)文档，其实Gulp API就这么几个，没什么好可怕的。

## js代码校验、合并和压缩

希望大家已经知道如何去创建一个任务了，接下来我们完成scripts的校验、合并和压缩的任务吧：

    gulp.task('scripts', function() {
      return gulp.src('src/scripts/**/*.js')
        .pipe(jshint('.jshintrc'))
        .pipe(jshint.reporter('default'))
        .pipe(concat('main.js'))
        .pipe(gulp.dest('dist/assets/js'))
        .pipe(rename({suffix: '.min'}))
        .pipe(uglify())
        .pipe(gulp.dest('dist/assets/js'))
        .pipe(notify({ message: 'Scripts task complete' }));
    });

需要提醒的是我们要设置JSHint的reporter方式，上面使用的是default默认的，了解更多点击[这里]()。
## 压缩图片

    gulp.task('images', function() {
      return gulp.src('src/images/**/*')
        .pipe(imagemin({ optimizationLevel: 3, progressive: true, interlaced: true }))
        .pipe(gulp.dest('dist/assets/img'))
        .pipe(notify({ message: 'Images task complete' }));
    });

这个任务使用`imagemin`插件把所有在`src/images/`目录以及其子目录下的所有图片（文件）进行压缩，我们可以进一步优化，利用缓存保存已经压缩过的图片，使用之前装过的gulp-cache插件，不过要修改一下上面的代码：

将这行代码:

    .pipe(imagemin({ optimizationLevel: 3, progressive: true, interlaced: true }))

修改成:

    .pipe(cache(imagemin({ optimizationLevel: 5, progressive: true, interlaced: true })))

现在，只有新建或者修改过的图片才会被压缩了。
## 清除文件

在任务执行前，最好先清除之前生成的文件：

    gulp.task('clean', function(cb) {
        del(['dist/assets/css', 'dist/assets/js', 'dist/assets/img'], cb)
    });

在这里没有必要使用Gulp插件了，可以使用NPM提供的插件。我们用一个回调函数（`cb`）确保在退出前完成任务。

## 设置默认任务（default）

我们在命令行下输入`$ gulp`执行的就是默认任务，现在我们为默认任务指定执行上面写好的三个任务：

    gulp.task('default', ['clean'], function() {
        gulp.start('styles', 'scripts', 'images');
    });

在这个例子里面，clean任务执行完成了才会去运行其他的任务，在gulp.start()里的任务执行的顺序是不确定的，所以将要在它们之前执行的任务写在数组里面。

## 监听文件

为了监听文件的是否修改以便执行相应的任务，我们需要创建一个新的任务，然后利用`gulp.watchAPI`实现：	

    gulp.task('watch', function() {
      // Watch .scss files
      gulp.watch('src/styles/**/*.scss', ['styles']);
      // Watch .js files
      gulp.watch('src/scripts/**/*.js', ['scripts']);
      // Watch image files
      gulp.watch('src/images/**/*', ['images']);
    });

我们将不同类型的文件分开处理，执行对应的数组里的任务。现在我们可以在命令行输入$ gulp watch执行监听任务，当`.sass`、`.js`和图片修改时将执行对应的任务。

## 自动刷新页面

Gulp也可以实现当文件修改时自动刷新页面，我们要修改watch任务，配置LiveReload：

    gulp.task('watch', function() {
      // Create LiveReload server
      livereload.listen();
      // Watch any files in dist/, reload on change
      gulp.watch(['dist/**']).on('change', livereload.changed);
    });

要使这个能够工作，还需要在浏览器上安装LiveReload插件，除此之外还能这样做

## 所有任务放一起

    /*!
     * gulp
     * $ npm install gulp-ruby-sass gulp-autoprefixer gulp-minify-css gulp-jshint gulp-concat gulp-uglify gulp-imagemin gulp-notify gulp-rename gulp-livereload gulp-cache del --save-dev
     */
    // Load plugins
    var gulp = require('gulp'),
        sass = require('gulp-ruby-sass'),
        autoprefixer = require('gulp-autoprefixer'),
        minifycss = require('gulp-minify-css'),
        jshint = require('gulp-jshint'),
        uglify = require('gulp-uglify'),
        imagemin = require('gulp-imagemin'),
        rename = require('gulp-rename'),
        concat = require('gulp-concat'),
        notify = require('gulp-notify'),
        cache = require('gulp-cache'),
        livereload = require('gulp-livereload'),
        del = require('del');
    // Styles
    gulp.task('styles', function() {
      return gulp.src('src/styles/main.scss')
        .pipe(sass({ style: 'expanded', }))
        .pipe(autoprefixer('last 2 version', 'safari 5', 'ie 8', 'ie 9', 'opera 12.1', 'ios 6', 'android 4'))
        .pipe(gulp.dest('dist/styles'))
        .pipe(rename({ suffix: '.min' }))
        .pipe(minifycss())
        .pipe(gulp.dest('dist/styles'))
        .pipe(notify({ message: 'Styles task complete' }));
    });
    // Scripts
    gulp.task('scripts', function() {
      return gulp.src('src/scripts/**/*.js')
        .pipe(jshint('.jshintrc'))
        .pipe(jshint.reporter('default'))
        .pipe(concat('main.js'))
        .pipe(gulp.dest('dist/scripts'))
        .pipe(rename({ suffix: '.min' }))
        .pipe(uglify())
        .pipe(gulp.dest('dist/scripts'))
        .pipe(notify({ message: 'Scripts task complete' }));
    });
    // Images
    gulp.task('images', function() {
      return gulp.src('src/images/**/*')
        .pipe(cache(imagemin({ optimizationLevel: 3, progressive: true, interlaced: true })))
        .pipe(gulp.dest('dist/images'))
        .pipe(notify({ message: 'Images task complete' }));
    });
    // Clean
    gulp.task('clean', function(cb) {
        del(['dist/assets/css', 'dist/assets/js', 'dist/assets/img'], cb)
    });
    // Default task
    gulp.task('default', ['clean'], function() {
        gulp.start('styles', 'scripts', 'images');
    });
    // Watch
    gulp.task('watch', function() {
      // Watch .scss files
      gulp.watch('src/styles/**/*.scss', ['styles']);
      // Watch .js files
      gulp.watch('src/scripts/**/*.js', ['scripts']);
      // Watch image files
      gulp.watch('src/images/**/*', ['images']);
      // Create LiveReload server
      livereload.listen();
      // Watch any files in dist/, reload on change
      gulp.watch(['dist/**']).on('change', livereload.changed);
    });

在[gist](https://gist.github.com/markgoodyear/8497946#file-gruntfile-js)上有源码，并且附上Grunt的实现作为对比。
参考链接：[http://markgoodyear.com/2014/01/getting-started-with-gulp/](http://markgoodyear.com/2014/01/getting-started-with-gulp/)

原文；【[https://markpop.github.io/2014/09/17/Gulp%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B/](https://markpop.github.io/2014/09/17/Gulp%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B/)】

## 在gulp中顺序执行任务 

gulp是前端开发过程中对代码进行构建(Build)的工具，类似于Java世界中的Ant或者Maven。与Ant或Maven类似，在用gulp进行build时，经常需要顺序执行任务：在前一个任务彻底结束后才开始下一个任务。比如，在进行新的一次LESS编译前，首先需要保证删除上一次编译的结果。也即，对于以下两个gulp任务：

    var gulp = require('gulp'),
        less = require('gulp-less'),
        clean = require('gulp-clean');
    
    gulp.task('compileLESS', function(){
        gulp.src('sample.less')
            .pipe(less())
            .pipe(gulp.dest('sample.css'));
    });
    
    gulp.task('clean', function(){
        gulp.src('sample.css', { read:false })
            .pipe(clean());
    });

必须保证’clean’任务执行完毕后才开始进行’compileLESS’任务。

## gulp中的顺序执行方案

在gulp对任务的定义中(gulp.task)，可以声明任务之间的依赖。比如，可以声明任务’compileLESS’依赖于’clean’:

    gulp.task('compileLESS', ['clean'], function(){
        //compile LESS file...
    });
    gulp.task('clean', function(){
        gulp.src('sample.css', { read:false })
            .pipe(clean());
    });

在声明任务依赖后，可以保证’clean’定义的function执行完毕后，’compileLESS’定义的function才开始执行。

不过，即使定义了任务依赖，对于上述例子我们依然会发现：有时，需要清理的文件尚未删除干净，用于编译的任务就已经开始生成文件了；这在文件较多的项目环境下尤为常见。原因在于，对’clean’定义的function而言，虽然函数本身已经执行完毕了，但是文件删除操作可能仍在进行 — gulp任务中的操作大多数都是数据流(Stream)的操作，其操作进度与函数执行无关。

如果需要在文件彻底清理后才开始执行’compileLESS’任务，则需要在’clean’任务中进行特殊编码：令其返回最终的数据流(Stream)对象：

    gulp.task('compileLESS', ['clean'], function(){
        //compile LESS file...
    });
    gulp.task('clean', function(){
        return gulp.src('sample.css', { read:false })
            .pipe(clean());
    });

**问题根源：**

应该承认，这样的一种依赖定义方式是不直观的、令人困惑的。然而思考之后会发现，对于这个问题，不能简单的用”bug”来进行总结。

问题的难点在于：如何在一个任务运行系统中监听数据流的结束？对于数据流而言，代码语句的执行结束仅仅意味着数据操作的开始，唯一能确定数据操作结束的是最后一个数据流所触发的end事件；因此，只有想办法监听到这个end事件，才有可能实现真正意义上的任务依赖。而在任务定义的函数中返回最后一个数据流，是一个相对来说使用起来最方便的方案。

事实上，gulp中的任务运行系统并不是自己实现的，而是直接使用了orchestrator。在gulp的源代码中可以发现，gulp继承了orchestrator，而gulp.task仅仅只是orchestrator.add的别名而已：

    //gulp source code
    var util = require('util');
    var Orchestrator = require('orchestrator');
    
    function Gulp() {
      Orchestrator.call(this);
    }
    util.inherits(Gulp, Orchestrator);
    
    Gulp.prototype.task = Gulp.prototype.add;
在orchestrator中，解决上述任务依赖的方式有三种：

   

 1. 在任务定义的function中返回一个数据流，当该数据流的end事件触发时，任务结束。
 2. 在任务定义的function中返回一个promise对象，当该promise对象resolve时，任务结束。
 3. 在任务定义的function中传入callback变量，当callback()执行时，任务结束。

gulp脚本中可以使用这三种方法来实现任务依赖，不过由于gulp中的任务大多是数据流操作，因此以第一种方法为主。