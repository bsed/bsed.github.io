---
title: 用JS 模仿微信的摇一摇功能DeviceMotionEvent 
date: 2013-11-18 22:22:00
updated: 2015-11-07 22:24:57
tags: 
- ubuntu
- emacs
categories: 
- linux

---
DeviceMotionEvent

DeviceMotionEvent [apple参考文档](https://developer.apple.com/library/safari/documentation/SafariDOMAdditions/Reference/DeviceMotionEventClassRef/DeviceMotionEvent/DeviceMotionEvent.html) 会在设备发生有意义的摆动(或运动)时产生。事件对象封装有产生的间距值，旋转率，和设备加速度。

    var color = new Array('#fff', '#ff0', '#f00', '#000', '#00f', '#0ff');
    if(window.DeviceMotionEvent) {
        var speed = 105;
        var x = y = z = lastX = lastY = lastZ = 0;
        var ys=1;
        window.addEventListener('devicemotion', function(){
            var acceleration =event.accelerationIncludingGravity;
            x = acceleration.x;
            y = acceleration.y;
            if(Math.abs(x-lastX) > speed || Math.abs(y-lastY) > speed) {
                ys+=1;
                alert("你摇一摇换了桌面哦")
                document.getElementById("text").innerHTML+=ys+"<br />";
                document.body.style.backgroundColor = color[Math.round(Math.random()*10)%6];
            }
            lastX = x;
            lastY = y;
        }, false);
    }
    
    
    <!--more-->
    
    
    if (window.DeviceMotionEvent) {
        window.addEventListener('devicemotion', deviceMotionHandler, false);
    } else {
        document.getElementById("dmEvent").innerHTML = "Not supported on your device."
    }
    function deviceMotionHandler(eventData) {
        // 捕捉重力加速度
        var acceleration = eventData.accelerationIncludingGravity;
        
        // 打印加速数据
        var rawAcceleration = "[" +  Math.round(acceleration.x) + ", " +
          Math.round(acceleration.y) + ", " + Math.round(acceleration.z) + "]";
        
        // Z轴,可知设备朝上或者朝下
        var facingUp = -1;
        if (acceleration.z &gt; 0) {
          facingUp = +1;
        }
     
        // 根据重力通过 acceleration.x|y 转换得到加速值,
        // 运用重力加速度9.81来计算得到一个百分比然后乘以转换角度90
        var tiltLR = Math.round(((acceleration.x) / 9.81) * -90);
        var tiltFB = Math.round(((acceleration.y + 9.81) / 9.81) * 90 * facingUp);
        
        // 打印加速度的计算结果
        document.getElementById("moAccel").innerHTML = rawAcceleration;
        document.getElementById("moCalcTiltLR").innerHTML = tiltLR;
        document.getElementById("moCalcTiltFB").innerHTML = tiltFB;
        
        // 将2D和3D的转换应用到图片上
        var rotation = "rotate(" + tiltLR + "deg) rotate3d(1,0,0, " + (tiltFB) + "deg)";
        document.getElementById("imgLogo").style.webkitTransform = rotation;
    }