---
title: 使用JS加载CSS
date: 2013-10-23 09:56:00
updated: 2015-11-03 21:06:58
tags: 
- mysql
- lleftjoin
categories: 
- sql

---
    // js动态加载css文件
    function loadjscssfile(filename,filetype){
        if(filetype == "css"){
            var fileref = document.createElement('link');
            fileref.setAttribute("rel","stylesheet");
            fileref.setAttribute("type","text/css");
            fileref.setAttribute("href",filename);
        }
        document.getElementsByTagName("head")[0].appendChild(fileref);
    }
    loadjscssfile("test.css","css");