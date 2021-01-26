---
title: "grunt小试牛刀"
categories: [ "JS" ]
tags: [ "js","grunt" ]
draft: false
slug: "cs-grunt"
date: "2014-08-11 08:53:00"
---

### grunt 第一次尝试

# 起因
最近写了一个[angular-cuf-nav][0]的项目，项目中的指令需要和其相应的模板build成一个js文件，以减少http请求。

## 什么是grunt
Grunt 是一个基于任务的JavaScript工程命令行构建工具。

## [grunt入门][2]

## [grunt方法][1]
- grunt.initConfig方法


<!--more-->


  用于模块配置，它接受一个对象作为参数。该对象的成员与使用的同名模块一一对应。
 每个目标的具体设置，需要参考该模板的文档。就cssmin来讲，minify目标的参数具体 含义如下：
		expand：如果设为true，就表示下面文件名的占位符（即*号）都要扩展成具体的文件名。
		cwd：需要处理的文件（input）所在的目录。
		src：表示需要处理的文件。如果采用数组形式，数组的每一项就是一个文件名，可以使用通配符。
		dest：表示处理后的文件名或所在目录。
		ext：表示处理后的文件后缀名。
 
- grunt常用函数说明：

	- grunt.initConfig：定义各种模块的参数，每一个成员项对应一个同名模块。
	- grunt.loadNpmTasks：加载完成任务所需的模块。
	- grunt.registerTask：定义具体的任务。第一个参数为任务名，第二个参数是一个数组， 表示该任务需要依次使用的模块。

- grunt常用模块：

	- grunt-contrib-clean：删除文件。
	- grunt-contrib-compass：使用compass编译sass文件。
	- grunt-contrib-concat：合并文件。
	- grunt-contrib-copy：复制文件。
	- grunt-contrib-cssmin：压缩以及合并CSS文件。
	- grunt-contrib-imagemin：图像压缩模块。
	- grunt-contrib-jshint：检查JavaScript语法。
	- grunt-contrib-uglify：压缩以及合并JavaScript文件。
	- grunt-contrib-watch：监视文件变动，做出相应动作。

##  Example
package.json
```javascript
{
  "name": "angular-cuf-nav",
  "version": "0.0.0",
  "description": "Angular Navigation Menu",
  "author": "",
  "license": "MIT",
  "devDependencies": {
    "bower": "^1.3.12",
    "grunt": "^0.4.5",
    "grunt-contrib-clean": "^0.6.0",
    "grunt-contrib-concat": "^0.5.0",
    "grunt-contrib-copy": "^0.7.0",
    "grunt-contrib-cssmin": "^0.11.0",
    "grunt-contrib-htmlmin": "^0.3.0",
    "grunt-contrib-uglify": "^0.7.0",
    "grunt-html2js": "^0.3.0",
    "grunt-ngmin": "0.0.3",
    "load-grunt-tasks": "^3.1.0",
    "time-grunt": "^1.0.0"
  }
}

```
Gruntfile.js
```javascript
module.exports = function(grunt) {

  // Load grunt tasks automatically 读取你的packagejson自动加载grunt task
  require('load-grunt-tasks')(grunt);

  // Time how long tasks take. Can help when optimizing build times
  require('time-grunt')(grunt);
 
 // Project configuration.
 // 注意 <%= pkg.name %> 替换变量
 // 定义grunt task的配置
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    banner: '/*! <%= pkg.name %> by hjzheng <%= grunt.template.today("yyyy-mm-dd") %> */\n',
    uglify: {
      options: {
        banner: '<%= banner %>'
      },
      build: {
        src: 'tmp/<%= pkg.name %>.all.js',
        dest: 'build/js/<%= pkg.name %>.min.js'
      }
    },
    cssmin: {
      options: {
        banner: '<%= banner %>' 
      },
      build: {
        src: 'src/css/<%= pkg.name %>.css',
        dest: 'build/css/<%= pkg.name %>.min.css'
      }   
    },
    //将template转换成JS
    html2js: {
      options: {
        module: 'cuf-nav-template'
      },
      main: {
        src: ['src/template/*.html'],
        dest: 'tmp/templates.js'
      }
    },
    copy: {
      main: {
         files:[
           {expand: true, cwd: 'src/js', src: ['*.js'], dest: 'tmp/'}
         ]
      }
    },
    concat: {
      main: {
       src: ['tmp/templates.js', 'tmp/<%= pkg.name %>.js'],
       dest: 'tmp/<%= pkg.name %>.all.js'
      }  
    }, 
    clean: {
      all: ['tmp', 'build/*'],
      tmp: ['tmp']
    },
    //处理uglify的bug for angular，将ngmin放在它前面处理 
    ngmin: {
      main: {
        src: 'tmp/<%= pkg.name %>.all.js',
        dest: 'tmp/<%= pkg.name %>.all.js'
      }
    }
  });

  //Load the plugin that provides the task. 手动加载grunt task
  //grunt.loadNpmTasks('grunt-contrib-uglify');
  //grunt.loadNpmTasks('grunt-contrib-cssmin');
  
  // Default task(s). 设置自己的task
  grunt.registerTask('default', 
               [
                 'clean:all', 
                 'copy:main', 
                 'html2js:main', 
                 'concat:main', 
                 'ngmin', 
                 'uglify', 
                'cssmin'
              ] 
  );
};
```
## [详细Grunt使用][3]

[0]:https://github.com/hjzheng/angular-cuf-nav
[1]:http://www.cnblogs.com/hubcarl/p/4095122.html
[2]:http://www.gruntjs.org/docs/getting-started.html
[3]:http://yanhaijing.com/grunt/2014/07/14/building-a-javascript-library-with-grunt-js/
