---
title: JS/NodeJS 格式化Console 下的log
date: 2015-11-01 11:53:00
updated: 2015-11-01 11:53:51
tags: 
- tomcat
categories: 
- java

---
    var util = require('util');
    
    console.flog = function(data) {
        var options = {
            showHidden: false,
            depth: null,
            colors: true
        };
        return console.log(util.inspect(data, options));
    };