---
title: Javascript函数高阶性能——柯里化
date: 2014-11-10 17:03:00
updated: 2015-11-07 17:05:52
tags: 
- web
- scroll
- solutions
categories: 
- js

---
## 柯里化

维基百科的定义：在计算机科学中，柯里化（Currying）是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

    var foo = function(a) {
    	return function(b) {
    		return a * a + b * b;
    	}
    }
    
    var result1 = foo(3)(4);
    var result2 = (foo(3))(4);
    alert(result1)  // 3*3+4*4 = 25
    alert(result2)  // 3*3+4*4 = 25


<!--more-->


再来另外一个例子：

    function add(seed){
    	function retVal(later){
    		return add(seed + later);
    	}
    	retVal.toString = function(){
    		return seed;
    	}
    	return retVal;
    }
    
    var result = add(5)(8)(9)(2)(1);
    alert(result);

如果你不是很容易看懂的话，其实有方法可以用，比如可以插入一些代码看看函数具体是怎么运行的，如下：

    function add(seed){
    	console.log("步骤一" + seed);
    	function retVal(later){
    		console.log("步骤二" + seed);
    		console.log("步骤三" + later);
    		return add(seed + later);
    	}
    	retVal.toString = function(){
    		return seed;
    	}
    	return retVal;
    }
    
    var result = add(5)(8)(9)(2)(1);
    alert(result);

一般的currying函数只有两重，执行情况如下，第一次执行参数不足返回内部函数，第二次执行才最终完成。然后循环执行完所有参数