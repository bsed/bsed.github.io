---
title: "使用window.open 居中弹窗"
categories: [ "JS" ]
tags: [ "js","window.open" ]
draft: false
slug: "the-use-of-windowopen-in-the-window"
date: "2013-10-29 08:26:00"
---

    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
     <head>
      <title>web开发居中弹窗</title>
      <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
      <script type="text/javascript">
      <!--
    	function openwindow(url,name,iWidth,iHeight){
    	  var url;                                 //转向网页的地址;
    	  var name;                           //网页名称，可为空;
    	  var iWidth;                          //弹出窗口的宽度;
    	  var iHeight;                        //弹出窗口的高度;
    	  var iTop = (window.screen.availHeight-30-iHeight)/2;       //获得窗口的垂直位置;
    	  var iLeft = (window.screen.availWidth-10-iWidth)/2;           //获得窗口的水平位置;
    	  window.open(url,name,'height='+iHeight+',,innerHeight='+iHeight+',width='+iWidth+',innerWidth='+iWidth+',top='+iTop+',left='+iLeft+',toolbar=no,menubar=no,scrollbars=auto,resizeable=no,location=no,status=no');
    	 }
      //-->
      </script>
     </head>
    
    
    <!--more-->
    
    
     <body>
      <input type="button" value="进入Yigr主页" onclick="openwindow('http://yigrherb.com','web开发',800,600)"/>
     </body>
    </html>

