---
title: 商品定时秒杀倒计时代码
date: 2013-07-03 15:57:00
updated: 2015-08-03 15:59:05
tags: 
- css
categories: 
- css

---
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
    <html>
    <head>
    <title>商品倒计时</title>
    <meta http-equiv="Content-Type" content="text/html; charset=gb2312">
    </head>


<!--more-->


    <body>
    <p>商品定时抢购倒计时中...</p>
    <p>此商品抢购时间还有<span id="_lefttime" style="color:red;font-size:18pt;"></span></p>
    <SCRIPT LANGUAGE="JavaScript">
    function _fresh()
    {
            var endtime=new Date("December 20, 2010 22:00:00");
            var nowtime = new Date();
            var leftsecond=parseInt((endtime.getTime()-nowtime.getTime())/1000);
            if(leftsecond<0){leftsecond=0;}
            __d=parseInt(leftsecond/3600/24);
            __h=parseInt((leftsecond/3600)%24);
            __m=parseInt((leftsecond/60)%60);
            __s=parseInt(leftsecond%60);
            document.getElementById("_lefttime").innerHTML=__d+"天 "+__h+"小时"+__m+"分"+__s+"秒";
    }
    _fresh()
    setInterval(_fresh,1000);
    </SCRIPT>
    </body>
    </html>