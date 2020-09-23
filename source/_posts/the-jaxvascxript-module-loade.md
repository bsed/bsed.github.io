---
title: JavaScript模块化加载器
date: 2016-03-15 21:08:00
updated: 2016-03-15 21:12:38
tags: 
- nodejs
- vscode
- typescript
categories: 
- js

---
对任何程序，都存在一个规模的问题，起初我们使用函数来组织不同的模块，但是随着应用规模的不断变大，简单的重构函数并不能顺利的解决问题。尤其对JavaScript程序而言，模块化有助于解决我们在前端开发中面临的越来越复杂的需求。

## 为什么需要模块化

对开发者而言，有很多理由去将程序拆分为小的代码块。这种模块拆分的过程有助于开发者更清晰的阅读和编写代码，并且能够让编程的过程更多的集中在模块的功能实现上，和算法一样，分而治之的思想有助于提高编程生产率。

在本文中，我们将集中讨论JavaScript的模块化开发，并实现一个简单的module loader。对于模块化的基础知识，可以参考阅读[这篇文章](http://eloquentjavascript.net/10_modules.html)。
<!--more-->
## 实现模块化

### 使用函数作为命名空间

在JavaScript中，函数是唯一的可以用来创建新的作用域的途径。考虑到一个最简单的需求，我们通过数字来获得星期值，例如通过数字0得到星期日，通过数字1得到星期一。我们可以编写如下的程序：

	var names = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
	
	function dayName(number) {
	    return names[number];
	}

	console.log(dayName(1));

上面的程序，我们创建了一个函数`dayName()`来获取星期值。但问题是，`names`变量被暴露在全局作用域中。更多的时候，我们希望能够构造私有变量，而暴露公共函数作为接口。

对JavaScript中的函数而言，我们可以通过创建立即调用的函数表达式来达到这个效果，我们可以通过如下的方式重构上面的代码，使得私有作用域成为可能：
	
	var dayName = function() {
	    var names = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
	
	    return {
	        name: function(number) {
	            return names[number];
	        },
	
	        number: function(name) {
	            return names.indexOf(name);
	        }
	    };
	}();
	
	console.log(dayName.name(3));
	console.log(dayName.number("Sunday"));
	
上面的程序中，我们通过将变量包括在一个函数中，这个函数会立即执行，并返回一个包含两个属性的对象，返回的对象会被赋值给`dayName`变量。在后面，我们可以通过`dayName`变量来访问暴露的两个函数接口`name`和`number`。

对代码进一步改进，我们可以利用一个`exports`对象来达到暴露公共接口的目的，这种方法可以通过如下方法实现，代码如下：

	var weekDay = {};

	(function(exports) {
	    var names = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
	
	    exports.name = function(number) {
	        return names[number];
	    };
	
	    exports.number = function(name) {
	        return names.indexOf(name);
	    };
	
	})(weekDay); // outside of a function, this refers to the global scope object
	
	console.log(weekDay.name(weekDay.number("Saturday")));

上面的这种模块构造方式在以浏览器为核心的前端编码中非常常见，通过暴露一个全局变量的方式来将代码包裹在私有的函数作用域中。但这种方法依然会存在问题，在复杂应用中，你依然无法避免同名变量。

### 从全局作用域中分离，实现`require`方法

更进一步的，为了实现模块化，我们可以通过构造一个系统，使得一个函数可以`require`另一个函数的方式来实现模块化编程。所以我们的目标是，实现一个`require`方法，通过传入模块名来取得该模块的调用。这种实现方式要比前面的方法更为优雅的体现模块化的理念。对`require`方法而言，我们需要完成两件事。

1. 我们需要实现一个`readFile`方法，它能通过给定字符串返回文件的内容。
2. 我们需要能够将返回的字符串作为代码进行执行。

我们假设已经存在了`readFile`这个方法，我们更加关注的是如何能够将字符串作为可执行的程序代码。通常我们有好几种方法来实现这个需求，最常见的方法是`eval`操作符，但我们常常在刚学习JavaScript的时候被告知，使用`eval`是一个非常不明智的决策，因为使用它会导致潜在的安全问题，因此我们放弃这个方法。

一个更好的方法是使用`Function`构造器，它需要两个参数：使用逗号分隔的参数列表字符串，和函数体字符串。例如：

	var plusOne = new Function("n", "return n+1");
	console.log(plusOne(5)); // 6

下面我们可以来实现`require`方法了：

	// module.js
	function require(name) {
		
		// 调用一个模块，首先检查这个模块是否已被调用
	    if(name in require.cache) {
	        return require.cache[name];
	    }
	
	    var code = new Function("exports, module", readFile(name));
	    var exports = {},
	        module = {exports: exports};
	    code(exports, module);
	
	    require.cache[name] = module.exports;
	    return module.exports;
	}

	// 缓存对象，为了应对重复调用的问题	
	require.cache = Object.create(null);

	// todo:
	function readFile(fileName) { ... }

在页面中使用`require`函数：
	
	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>demo</title>
	    <script src="module.js"></script>
	</head>
	<body>
	    <script>
	        var weekDay = require("weekDay");
	        var today = require("today");
	
	        console.log(weekDay.name(today.dayNumber()));
	    </script>
	</body>
	</html>

通过这种方式实现的模块化系统通常被称为是`CommonJS模块`风格的，Node.js正式使用了这种风格的模块化系统。这里只是提供了一个最简单的实现方法，在真实应用中会有更加精致的实现方法。

### 慢载入模块和AMD

对浏览器编程而言，通常不会使用`CommonJS`风格的模块化系统，因为对于Web而言，加载一个资源远没有在服务端来的快，这受到网络性能的影响，尤其一个模块如果过大的话，可能会阻塞当前的程序（JS是顺序执行的）。[Browserify](http://browserify.org/)是解决这个问题的一个比较出名的模块化方案。

> 这个过程大概是这样的：首先检查模块中是否存在`require`其他模块的语句，如果有，就解析所有相关的模块，然后组装为一个大模块，通常我们称这个过程为线下构建，这样页面可以直接引用这个组装后的大资源。

模块化的另一个解决方案是使用AMD，即异步模块定义，AMD允许通过异步的方式加载模块内容，这样就不会阻塞代码的执行。关于AMD的相关背景知识，可以参考[这篇文章](http://requirejs.org/docs/whyamd.html)。

我们想要实现的功能大概是这个样子的：

	// index.html 中的部分代码
	define(["weekDay.js", "today.js"], function (weekDay, today) {
	        console.log(weekDay.name(today.dayNumber()));
	        document.write(weekDay.name(today.dayNumber()));
	    });

问题的核心是实现`define`方法，它的第一个参数是定义该模块说需要的依赖列表，参数而是该模块的具体工作函数。一旦所依赖的模块都被加载后，`define`便会执行参数2所定义的工作函数。`weekDay`模块的内容大概是下面的内容：

	// weekDay.js
	define([], function() {
	    var names = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
	
	    return {
	        name: function(number) { return names[number]},
	        number: function(name) { return names.indexOf(name)}
	    }
	});

下面我们来关注如何实现`define()`方法。为了实现这个方法，我们需要定义一个`backgroundReadFile()`方法来异步的获取文件内容。此外我们需要能够监视模块的加载状态，当模块加载完后能够告诉函数去执行具体的工作函数（回调）。

	// 通过Ajax来异步加载模块
	function backgroundReadFile(url, callback) {
	    var req = new XMLHttpRequest();
	    req.open("GET", url, true);
	    req.addEventListener("load", function () {
	        if (req.status < 400)
	            callback(req.responseText);
	    });
	    req.send(null);
	}

通过实现一个`getModule`函数，通过给定的模块名进行模块的调度运行工作。此外，为了提高性能，我们需要通过缓存的方式避免同一个模块被重复的载入。实现代码如下：

	// module.js 的部分内容
	var defineCache = Object.create(null);
	var currentMod = null;
	
	function getModule(name) {
	    if (name in defineCache) {
	        return defineCache[name];
	    }
	
	    var module = {
	        exports: null,
	        loaded: false,
	        onLoad: []
	    };
	
	    defineCache[name] = module;
	    backgroundReadFile(name, function(code) {
	        currentMod = module;
	        new Function("", code)();
	    });
	    return module;
	}

有了`getModule()`了函数之后，`define`方法可以借助该方法来为当前模块的依赖列表获取或创建模块对象。`define`方法的简单实现如下：

	// module.js 的部分内容
	function define(depNames, moduleFunction) {
	    var myMod = currentMod;
	    var deps = depNames.map(getModule);
	
	    deps.forEach(function(mod) {
	        if(!mod.loaded) {
	            mod.onLoad.push(whenDepsLoaded);
	        }
	    });
	
		// 用于检查是否所有的依赖模块都被成功加载了
	    function whenDepsLoaded() {
	        if(!deps.every(function(m) { return m.loaded; })) {
	            return;
	        }
	
	        var args = deps.map(function(m) { return m.exports; });
	
	        var exports = moduleFunction.apply(null, args);
	        if (myMod) {
	            myMod.exports = exports;
	            myMod.loaded = true;
	            myMod.onLoad.forEach(function(f) { f(); });
	        }
	    }
	
	    whenDepsLoaded();
	}

关于AMD的最出名的实现是[RequireJS](http://requirejs.org/)，它提供了AMD风格的更加全面的实现方式。

## 小结

模块通过将代码分离为不同的文件和命名空间，为大型程序提供了清晰的结构。通过构建良好的接口可以使得开发者更加建议的阅读、使用、扩展代码。尤其对JavaScript语言而言，由于天生的缺陷，使得模块化更加有助于程序的组织。在JavaScript的世界，有两种流行的模块化实现方式，一种称为CommonJS，通常是服务端的模块化实现方案，另一种称为AMD，通常针对浏览器环境。其他关于模块化的知识，你可以参考[这篇文章](https://github.com/wwsun/fe-knowledge-points#modular)。

## References

1. Eloquent JavaScript, chapter 10, Modules
2. [Browserify运行原理分析](http://www.alloyteam.com/2014/10/browserify-yun-xing-yuan-li-fen-xi/)
3. [Why AMD?](http://requirejs.org/docs/whyamd.html)
4. [JavaScript模块化知识点小结](https://github.com/wwsun/fe-knowledge-points#modular)

文章来源：[http://wwsun.github.io/posts/creating-javascript-modules-loader.html](http://wwsun.github.io/posts/creating-javascript-modules-loader.html)