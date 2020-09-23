---
title: 合并 Template 和 Javascript
date: 2014-09-06 11:00:00
updated: 2014-09-06 11:16:39
tags: 
- cdn
- lib
categories: 
- css

---
目前手头上负责的项目已经开发两年多了，刚开始那会儿，require.js 大家还不太熟悉，backbone 还没周岁，项目的模块化加载是基于 head.js 基础上加入了 css 加载的几近纯手工方式，打包的时候，需要根据用途进行JS合并。使用的是模版引擎是 underscore 自带的 tempalte。
项目原有的加载问题
模版的获取、渲染流程是这样的
<!--more-->
通过工具中定义了一个获取模版文件的方法`CustomGetTemplateFn`

    //utils.js
    var CustomGetTemplateFn = function (templateFileName){
    	var template = $.ajax({
    		url: "./template/" + templateFileName + ".tpl",
    		dataType: "script",
    		async: false
    	});
    	return _.template(template);
    }

图片模块中使用到了模版文件 book.html

     //BookModule.js
    var BookModule = function(){
        var el = CustomGetTemplateFn('book.html')(obj);
        $(el).appendTo($('body'));
    }

这里我们遇到了几个问题。
`CustomGetTemplateFn()` 方法中的 $.ajax() 使用了同步方法，这将导致步请求将锁住浏览器；
由于跨域限制，模版文件无法部署到 CDN 上；
`_.template()` 众所周知的性能较差；
还有其它的常见优化问题：文件压缩、请求合并、按需加载。
解决办法
我能想到的最简单解决办法就是：预编译、按模块加载模版文件实现粗粒度的按需加载。
最后，我决定使用 grunt-contrib-jst 将模版文件编译成 JST 文件，然后从模块代码中读出使用到的模版JST文件拼接到头部。于是我编写了 grunt-jst-concat 这个插件，通过是正则检索出 Javascript 代码中使用到的模版文件，并将之合并。
`Github: https://github.com/willerce/grunt-jst-concat`
###使用介绍###
#####安装#####
    npm install grunt-jst-concat
#####载入#####
    grunt.loadNpmTasks('grunt-jst-concat');
####配置 Task，示例：#####

    grunt.initConfig({
      jst_concat: {
        options: {
          separator_start: '/**start**/',
          separator_end: '/**end**/',
          jst_path: 'tmp/template/',
          ext: ".jst",
          pattern: /CustomGetTemplateFn\s*\(\'(.*?).html\'\)/g,
          replace: function(jst){
            jst = jst.replace('this.jst=this.jst||{},', "");
            jst = jst.replace(/\n|\r/g, "");
            return jst;
          }
        },
        files: {
          'tmp/modules/ShopModule.js': ['test/fixtures/modules/ShopModule.js']
        }
      },
    })

#####配置变量说明#####
separator_start, separator_end : 前后的分割符
jst_path : JST 文件目录
ext : JST 文件后缀名
pattern: 正则，找出使用到的模版列表
replace: 函数，用于做一些替换工作，比如你不希望重复声明命名空间什么的
修改模版文件获取方法
将 JST 模版放到 JS 中，就不需要有任何的模版文件请求了，所以需要改造一下你的模版文件获取方法

    //utils.js
    var CustomGetTemplateFn = function (templateFileName){
    	if (isPublish) {
    		//如果是发布状态，直接从 window["JST"] 取出模版JST返回
    		return window["JST"]["./template/" + templateFileName + ".tpl"]
    	}
    	var template = $.ajax({
    		url: "./template/" + templateFileName + ".tpl",
    		dataType: "script",
    		async: false
    	});
    	return _.template(template);
    }

Grunt 使用指南: http://yaml.sinaapp.com/js/Grunt-uses.html