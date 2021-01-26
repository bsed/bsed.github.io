---
title: "JavaScript页面跟随滚动条两侧漂浮广告"
categories: [ "JS" ]
tags: [ "js","广告" ]
draft: false
slug: "javascript-page-follow-the-scroll-bar-on-both-sides-of-the-floating-ads"
date: "2011-08-21 10:36:00"
---

# JavaScript页面跟随滚动条两侧漂浮广告


<!--more-->


```
//< ![CDATA[
var showad = true;
var theTop = 60;            //上端位置
var AdDivW = 100;       //宽度
var AdDivH = 300;       //高度
var PageWidth = 800;    //页面多少宽度象素下正好不出现左右滚动条
var MinScreenW = 1024;  //显示广告的最小屏幕宽度象素
var old = theTop;
var tipLeft;
var tipRight;
var AdContentHtmlLeftUrl = "http://fbzl.org/";  //左侧广告Url
var AdContentHtmlLeftImage = "http://blog.fbzl.org/wp-content/uploads/2013/05/IMG_1794.png";    //左侧广告Image
var AdContentHtmlRightUrl = "http://fbzl.org/"; //右侧广告Url
var AdContentHtmlRightImage = "http://blog.fbzl.org/wp-content/uploads/2013/05/IMG_1794.png";   //右侧广告Image
var ClosebuttonHtml = '<div align="right" style="position: absolute;top:0px;right:0px;margin:2px;padding:2px;z-index:2000;"><a href="javascript:;" onclick="hidead()" style="color:red;text-decoration:none;font-size:12px;">关闭</a>';
document.write ('<div id="JavascriptLeftDiv" style="position: absolute;border: 1px solid #336699;background-color:#EEEEE2;z-index:1000;width:'+AdDivW+'px;height:'+AdDivH+'px;top:'+theTop+'px;word-break:break-all;">'+ClosebuttonHtml+'<div><div align="center" style="color:green;font-size:23pt;font-family:黑体;"> <a href="'+AdContentHtmlLeftUrl+'" target="_blank"><img src="'+AdContentHtmlLeftImage+'" width="100" height="300" border="0"/></a></div></div></div>');
document.write ('<div id="JavascriptRightDiv" style="position: absolute;border: 1px solid #336699;background-color:#EEEEE2;z-index:1000;width:'+AdDivW+'px;height:'+AdDivH+'px;top:'+theTop+'px;word-break:break-all;">'+ClosebuttonHtml+'<div><div align="center" style="color:green;font-size:23pt;font-family:黑体;"> <a href="'+AdContentHtmlRightUrl+'" target="_blank"><img src="'+AdContentHtmlRightImage+'" width="100" height="300" border="0"/></a></div></div></div>');
 
function initFloatTips() {
    tipLeft = document.getElementById('JavascriptLeftDiv');
    tipRight = document.getElementById('JavascriptRightDiv');
    scall();
};
function scall(){
    var grantt = 20;
    if(!showad){return;}
    if (window.screen.width&lt;MinScreenW){
        alert("临时提示：\n\n显示器分辨率宽度小于"+MinScreenW+",不显示广告");
        showad = false;
        document.getElementById("Javascript.LeftDiv").style.display="none";
        document.getElementById("Javascript.RightDiv").style.display="none";
        return;
    }
    if (window.innerHeight) {
        pos = window.pageYOffset
    }
    else if (document.documentElement &amp;&amp; document.documentElement.scrollTop) {
        pos = document.documentElement.scrollTop
    }
    else if (document.body) {
        pos = document.body.scrollTop;
    }
    pos = pos - tipLeft.offsetTop + theTop;
    pos = tipLeft.offsetTop + pos / 10;
    if (pos &lt; theTop) pos = theTop;
    if (pos != old) {
        tipLeft.style.top = pos + "px";
        tipRight.style.top = pos + "px";
        grantt = 10;
    }
    old = pos;
    setTimeout(scall , grantt);
}
function hidead()
{
    showad = false;
    document.getElementById("JavascriptLeftDiv").style.display="none";
    document.getElementById("JavascriptRightDiv").style.display="none";
}
//-->;
initFloatTips();
```