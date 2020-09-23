---
title: JS判断访问设备(userAgent)
date: 2012-10-16 09:07:00
updated: 2015-10-25 09:07:19
tags: 
- mysql
- unauthenticated-use
categories: 
- sql

---
## JS判断访问设备(userAgent)的操作系统[包括win8]
    //JS判断访问设备(userAgent)加载不同页面。代码如下：
        function detectOS() {
            var sUserAgent = navigator.userAgent;
    
            var isWin = (navigator.platform === "Win32") || (navigator.platform === "Windows");
            var isMac = (navigator.platform === "Mac68K") || (navigator.platform === "MacPPC") || (navigator.platform === "Macintosh") || (navigator.platform === "MacIntel");
            var bIsIpad = sUserAgent.match(/ipad/i) === "ipad";
            var bIsIphoneOs = sUserAgent.match(/iphone os/i) === "iphone os";
            var isUnix = (navigator.platform === "X11") && !isWin && !isMac;
            var isLinux = (String(navigator.platform).indexOf("Linux") > -1);
            var bIsAndroid = sUserAgent.toLowerCase().match(/android/i) === "android";
            var bIsCE = sUserAgent.match(/windows ce/i) === "windows ce";
            var bIsWM = sUserAgent.match(/windows mobile/i) === "windows mobile";
            if (isMac)
                return "Mac";
            if (isUnix)
                return "Unix";
            if (isLinux) {
                if (bIsAndroid)
                    return "Android";
                else
                    return "Linux";
            }
            if(bIsCE || bIsWM){
                return 'wm';
            }
            
            if (isWin) {
                var isWin2K = sUserAgent.indexOf("Windows NT 5.0") > -1 || sUserAgent.indexOf("Windows 2000") > -1;
                if (isWin2K)
                    return "Win2000";
                var isWinXP = sUserAgent.indexOf("Windows NT 5.1") > -1 ||
                        sUserAgent.indexOf("Windows XP") > -1;
                if (isWinXP)
                    return "WinXP";
                var isWin2003 = sUserAgent.indexOf("Windows NT 5.2") > -1 || sUserAgent.indexOf("Windows 2003") > -1;
                if (isWin2003)
                    return "Win2003";
                var isWinVista = sUserAgent.indexOf("Windows NT 6.0") > -1 || sUserAgent.indexOf("Windows Vista") > -1;
                if (isWinVista)
                    return "WinVista";
                var isWin7 = sUserAgent.indexOf("Windows NT 6.1") > -1 || sUserAgent.indexOf("Windows 7") > -1;
                if (isWin7)
                    return "Win7";
                var isWin8 = sUserAgent.indexOf("Windows NT 6.2") > -1 || sUserAgent.indexOf("Windows 8") > -1;
                if (isWin8)
                    return "Win8";
            }
            return "other";
        }
        console.log("您的操作系统是：" + detectOS());
        //alert(detectOS());

## js判断当前的手机Android|iPhone|winphone

    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
     <head>
      <title> web开发-test</title>
      <script language="javascript">
    	window.onload = function () {
    		alert("1");
    		var u = navigator.userAgent;
    		if (u.indexOf('Android') > -1 || u.indexOf('Linux') > -1) {//安卓手机
    		alert("安卓手机");
    		// window.location.href = "mobile/index.html";
    		} else if (u.indexOf('iPhone') > -1) {//苹果手机
    		// window.location.href = "mobile/index.html";
    		alert("苹果手机");
    		} else if (u.indexOf('Windows Phone') > -1) {//winphone手机
    		alert("winphone手机");
    		// window.location.href = "mobile/index.html";
    		}else{
    			alert("你不在手机上运行这个页面");
    		}
    	}
    	</script>
     </head>
    
     <body>
      
     </body>
    </html>

