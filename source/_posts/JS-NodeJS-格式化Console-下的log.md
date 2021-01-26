---
title: "JS/NodeJS 格式化Console 下的log"
categories: [ "JS" ]
tags: [ "js","nodejs","console" ]
draft: false
slug: "log-jsnodejs-console"
date: "2015-11-01 11:53:00"
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