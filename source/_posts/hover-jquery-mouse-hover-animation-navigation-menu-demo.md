---
title: jQuery hover鼠标悬停动画导航菜单Demo
date: 2008-08-10 23:08:00
updated: 2015-10-25 08:32:04
tags: 
- mysql
- show
categories: 
- sql

---
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <title>导航菜单</title>
    <style>
    	body{background-image:url(http://www.webkfa.com/lzimg/bg.jpg);background-repeat:repeat;margin:0px;padding:0px;font-family:"微软雅黑", "宋体";font-size:12px;color:#CCCCCC;}
    .cls{clear:both;}
    a:focus{outline:none;}
    


<!--more-->


    
    /* menu */
    .menu{height:40px;display:block;padding:0px;width:800px;margin-top:0px;margin-right:auto;margin-bottom:40px;margin-left:auto;}
    .menu ul{list-style:none;padding:0;margin:0;}
    .menu ul li{/* width and height of the menu items */float:left;overflow:hidden;position:relative;line-height:40px;text-align:center;}
    .menu ul li a{/* must be postioned relative*/ position:relative;display:block;width:90px;height:40px;font-family:"微软雅黑", "宋体";font-size:12px;text-decoration:none;cursor:pointer;}
    .menu ul li a span{/* all layers will be absolute positioned */position:absolute;left:0;width:90px;}
    .menu ul li a span.out{top:0px;}
    .menu ul li a span.over,.menu ul li a span.bg{/* hide */top:-40px;}
    
    
    /** 1st example **/
    #menu1{background-color:#E8E8E8;background-image:url(http://www.webkfa.com/lzimg/menu_bg.gif);background-repeat:repeat-x;background-position:0px 0px;}
    #menu1 ul li a{color:#000;}
    #menu1 ul li a span.over{color:#FFF;}
    #menu1 ul li span.bg{/* height of the menu items */height:40px;background-image:url(http://www.webkfa.com/lzimg/bg_over.gif);background-repeat:no-repeat;background-position:center center;}
    
    
    /** 2nd example **/
    #menu2{background:#000;}
    #menu2 ul li a{color:#999999;}
    #menu2 ul li a span.over{color:#000;background-color:#f0f0f0;}
    </style>
    
    <script type="text/javascript" src="http://libs.baidu.com/jquery/1.9.0/jquery.js"></script>
    <script language="javascript">
    	$(document).ready(function() {
    
    		/* 	1st example	*/
    
    		/// wrap inner content of each anchor with first layer and append background layer
    		$("#menu1 li a").wrapInner( '<span class="out"></span>' ).append( '<span class="bg"></span>' );
    
    		// loop each anchor and add copy of text content
    		$("#menu1 li a").each(function() {
    			$( '<span class="over">' +  $(this).text() + '</span>' ).appendTo( this );
    		});
    
    		$("#menu1 li a").hover(function() {
    			// this function is fired when the mouse is moved over
    			$(".out",	this).stop().animate({'top':	'40px'},	250); // move down - hide
    			$(".over",	this).stop().animate({'top':	'0px'},		250); // move down - show
    			$(".bg",	this).stop().animate({'top':	'0px'},		120); // move down - show
    
    		}, function() {
    			// this function is fired when the mouse is moved off
    			$(".out",	this).stop().animate({'top':	'0px'},		250); // move up - show
    			$(".over",	this).stop().animate({'top':	'-40px'},	250); // move up - hide
    			$(".bg",	this).stop().animate({'top':	'-40px'},	120); // move up - hide
    		});
    				
    
    		/*	2nd example	*/
    		
    		$("#menu2 li a").wrapInner( '<span class="out"></span>' );
    		
    		$("#menu2 li a").each(function() {
    			$( '<span class="over">' +  $(this).text() + '</span>' ).appendTo( this );
    		});
    
    		$("#menu2 li a").hover(function() {
    			$(".out",	this).stop().animate({'top':	'40px'},	300); // move down - hide
    			$(".over",	this).stop().animate({'top':	'0px'},		300); // move down - show
    
    		}, function() {
    			$(".out",	this).stop().animate({'top':	'0px'},		300); // move up - show
    			$(".over",	this).stop().animate({'top':	'-40px'},	300); // move up - hide
    		});
    
    	});
    
    </script>
    </head>
    <body>
    <!-- 代码 开始 --><br />
    
    
    <div id="menu1" class="menu">
    	<ul>
    		<li><a href="#">首　　页</a></li>
    		<li><a href="#">企业文化</a></li>
    		<li><a href="#">产品展示</a></li>
    		<li><a href="#">新闻中心</a></li>
    		<li><a href="#">阳光服务</a></li>
    		<li><a href="#">加盟代理</a></li>
    		<li><a href="#">在线咨询</a></li>
    	</ul>
    	<div class="cls"></div>
    </div>
    
    <div id="menu2" class="menu">
    	<ul>
    		<li><a href="#">首　　页</a></li>
    		<li><a href="#">企业文化</a></li>
    		<li><a href="#">产品展示</a></li>
    		<li><a href="#">新闻中心</a></li>
    		<li><a href="#">阳光服务</a></li>
    		<li><a href="#">加盟代理</a></li>
    		<li><a href="#">在线咨询</a></li>
    	</ul>
    	<div class="cls"></div>
    </div>
    
    
    </body>
    </html>