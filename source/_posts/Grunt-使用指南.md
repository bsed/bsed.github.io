---
title: "Grunt 使用指南"
categories: [ "JS" ]
tags: [ "js","grunt" ]
draft: false
slug: "Grunt-uses"
date: "2014-09-06 10:36:00"
---

Grunt 是一个基于 NodeJS 的项目的自动化构建工。目前可以用的插件非常丰富，足以应对大多项目中的需要。CofeeScript, handlebars, jade, less, REQUIRE.JS 等知名开源项目都在使用。
日常开发中，我们常使用 Grunt 协助完成以下任务：
自定义项目模版、快速搭建

*代码压缩、合并、部署
代码校验、测试
静态服务器*


<!--more-->


###安装###
Grunt 目前的最新版本是 0.4.x，Grunt 和 Grunt 插件都使用 NPM 进行管理，需要安装于 Node.js 0.8.0 以上版本。NodeJS 安装请参考：[Windows，NodeJS 环境配置][1]
如果之前安装过 0.4.x 以下版本，需要先卸载。
 `npm uninstall -g grunt`
全局安装 Grunt-cli，安装完成，你即可以任意目录执行 grunt 命令
`npm install -g grunt-cli`
创建 Grunt 项目文件
一个 Grunt 项目，在项目根目录下，需要 package.json 和 Gruntfile.js 这两个文件。package.json 用于 NPM 包管理，Gruntfile 则用来定义你的任务。
###创建DEMO目录###

    mkdir grunt_demo_project
    cd grunt_demo_project

我们可以通过 grunt-init 来快速创建这两个文件，但这里为了快速入门，先手动创建。
package.json 内容

    {
      "name": "noderce",
      "version": "0.1.0",
      "description": "a nodejs blog",
      "homepage": "http://test.com/",
      "author": "Dzh <test@qq.com>",
      "devDependencies": {
        "grunt": "~0.4.1",
    	"grunt-contrib-clean": "~0.4.0",
        "grunt-contrib-nodeunit": "~0.1.2",
      }
    }

Gruntfile.js 内容

    module.exports = function(grunt) {
      // 配置构建任务
      grunt.initConfig({
        clean: {
    	  build: ["path/to/dir/one", "path/to/dir/two"],
    	  release: ["path/to/another/dir/one", "path/to/another/dir/two"]
    	},
    	nodeunit: {
          all: ['test/**/*_test.js']
    	}
      });
      //加载
      grunt.loadNpmTasks('grunt-contrib-clean');
      grunt.loadNpmTasks('grunt-contrib-nodeunit');
      // 组装构建任务
      grunt.registerTask('test', ['clean', 'nodeunit']);
    }

执行npm install 即可安装项目依赖的Grunt插件
`$ npm install`
大多时候，并不需要去修改 package.json，安装新的Grunt插件时带上 —save-dev 即可自动在 devDependencies 增加依赖包。
`npm install grunt-contrib-jslint --save-dev`
常用任务介绍
利用 JSHint 校验代码
安装
`npm install grunt-contrib-jshint --save-dev`
加载
`grunt.loadNpmTasks('grunt-contrib-jshint');`
###配置###
    // Project configuration.
    grunt.initConfig({
      jshint: {
        options: {
          curly: true,
          eqeqeq: true,
          eqnull: true,
          browser: true,
          globals: {
            jQuery: true
          },
        },
        uses_defaults: ['dir1/**/*.js', 'dir2/**/*.js'],
        with_overrides: {
          options: {
            curly: false,
            undef: true,
          },
          files: {
            src: ['dir3/**/*.js', 'dir4/**/*.js']
          },
        }
      },
    });

更多的配置选项，请参考：http://www.jshint.com/docs/
###Less 编译###
####安装####
`npm install grunt-contrib-less --save-dev`
####加载####
`grunt.loadNpmTasks('grunt-contrib-less');`
####配置####

    less: {
      development: {
        options: {
          paths: ["assets/css"]
        },
        files: {
          "path/to/result.css": "path/to/source.less"
        }
      },
      production: {
        options: {
          paths: ["assets/css"],
          yuicompress: true
        },
        files: {
          "path/to/result.css": "path/to/source.less"
        }
      }
    }

Grunt 任务的使用都非常简单，安装->加载->配置，三步曲搞定。配置在任务的 github 主页上都会说明得非常清楚。


  [1]: http://yaml.sinaapp.com/js/windows-install-nodejs-and-npm.html