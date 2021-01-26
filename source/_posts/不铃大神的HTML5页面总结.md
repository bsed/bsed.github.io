---
title: "不铃大神的HTML5页面总结"
categories: [ "Css" ]
tags: [ "css","html5" ]
draft: false
slug: "not-the-god-of-the-html5-page-summary-bell"
date: "2015-09-02 14:04:00"
---

## 1、页面字体大小，

    html {font-size:62.5%}
    /*未经调整的浏览器都符合: 1em=16px，所以10px=0.625em，设置html为62.5%，那1rem就是10px*/
    /*之后页面布局均使用rem来布局，方便统一调整*/

## 2、音乐播放操作

    <!--music dom; preload属性自行查阅-->
    <audio id="audio" src="./static/WeAreYoung.mp3" loop preload="auto" autoplay="true"></audio>


<!--more-->


js函数：

    (function(){
        //audio dom
        var videoMusic = $('#audio')[0];
     
        //监听回调
        var videoLoaded = function(){
            //控制按钮
            var $controll = $('#J_music-controller');
            //设置音量
            videoMusic.volume = 0.5;
            //开始播放
            videoMusic.play();
     
            $controll.show().on('tap', function(){
                var $this = $(this);
                if($this.hasClass('music-close')){
                    $this.removeClass('music-close');
                    videoMusic.play();
                }else{
                    $this.addClass('music-close');
                    videoMusic.pause();
                }
            });
        };
     
        //监听是否加载完成可以播放
        videoMusic.addEventListener('canplay', videoLoaded);
        if(videoMusic.readyState==4){
            videoLoaded();
        }
    }());

## 3、陀螺仪模拟摇一摇

    var AcceHelper = (function(){
        var lastAcceTime = 0, 
            lastAcceInfo = false,
            firstAcceInfo = false,
            lastDistance = 5000,
            isStart = false,
            stopTimer = 0,
            config = {
                timeLimit : 100, //两次感应间隔时间为200
                speedLimit : 80, //速度限制为80
                stopLimit : 400
            };
     
        var getDistance = function(a, l){
            var powDis = Math.pow(a.x-l.x, 2) + Math.pow(a.y-l.y, 2);
            return Math.sqrt(powDis);
        };
     
        var doAccelerationIncludingGravity = function(acceleration, oncallback, stopcallback){
            var curTime = (new Date()).getTime(),
                a = acceleration,
                l = lastAcceInfo,
                time = curTime-lastAcceTime;
     
            if(time>config.timeLimit && lastAcceInfo){
                //debugFunc('on acceleration call['+(a.x+'_'+a.y+'_'+a.z)+']');
     
                lastAcceTime = curTime;
     
                var distance = getDistance(a, l),
                    speed = distance/time*1000;
     
                if(speed>config.speedLimit){
                    isStart = true;
                    if(stopTimer){ //清除结束
                        clearTimeout(stopTimer);
                    }
     
                    if(!firstAcceInfo){ //设置第一次
                        firstAcceInfo = {
                            x : a.x,
                            y : a.y,
                            z : a.z
                        };
                    }
     
                    var aDistance = getDistance(a, firstAcceInfo);
                    debugFunc('on acceleration call['+(aDistance+'_'+lastDistance+'_'+distance)+']');
                    if(aDistance<lastDistance){ //回退的时候回调
                        oncallback && oncallback.call(null, {
                            distance : distance,
                            time : time,
                            speed : speed
                        }, a);
                    }
                    lastDistance = aDistance;
     
                }else if(isStart){
                    if(stopTimer){ //清除结束
                        clearTimeout(stopTimer);
                    }
     
                    isStart = false;
                    stopTimer = setTimeout(function(){
                        firstAcceInfo = false;
                        lastAcceInfo = false;
                        lastDistance = 5000;
     
                        debugFunc('on acceleration stop!'); //回调
                        stopcallback && stopcallback.call(null, {
                            distance : distance,
                            time : time,
                            speed : speed
                        }, a);
                    }, config.stopLimit);
                }
            }
     
            lastAcceInfo = {
                x : a.x,
                y : a.y,
                z : a.z
            };
        };
     
        var beginAccelerometer = function(oncallback, endcallback){
            if(false && mqq.support("mqq.sensor.startAccelerometer")){
                mqq.sensor.startAccelerometer(function(ret, x, y, z){
                    doAccelerationIncludingGravity({
                        x:x, y:y, z:z
                    }, oncallback, endcallback);
                });
                Helper.showAlert('启用Q的陀螺仪');
                return ;
            }
     
            //浏览器内调用自测用
            if(window.addEventListener){
                $(window).off('devicemotion').on('devicemotion',function(eventData){
                    doAccelerationIncludingGravity(eventData.accelerationIncludingGravity, oncallback, endcallback);
                    eventData.preventDefault();
                });
            }else{
                Helper.showAlert('不支持摇一摇功能');
            }
        };
     
        var endAccelerometer = function(oncall, endcall){
            var timer = 0,
                num = 0;
            beginAccelerometer(function(info, acceleration){
                num += 1;
                oncall && oncall.call(null, num, info, acceleration);
            }, function(info, acceleration){
                //$('#J_page-box-1').prepend('<div>[[[[['+num+'</div>');
                endcall && endcall.call(null, num, info, acceleration);
                num = 0;
            });
        };
     
        var removeAccelerometer = function(){
            $(window).off('devicemotion');
        };
     
        return {
            on  : endAccelerometer,
            remove : removeAccelerometer,
     
            init : function(options){
                $.extend(config, options);
            }
        };
    }());
     
    //使用方法
    AcceHelper.on(function(num, info, acceleration){
        //晃动第几次
        debugFunc('on acceleration num:' + num);
     
    }, function(num){ //结束
        //结束时回调总共摇动了几次
        debugFunc('on acceleration end num:' + num);
    });

分享一些动画
图片丢失:>

## 4、单行超出省略号方法
限定高宽， 设置css：overflow: hidden; text-overflow:ellipsis; white-space:nowrap;

## 5、多行超出省略号方法
css：overflow: hidden; display: -webkit-box; -webkit-line-clamp:2; -webkit-box-orient:vertical;

## 6、rgba的向下兼容方法
css：background:#ccc; background:rgba(0, 0, 0, 0.8);

## 7、获取窗口大小

    function getWindowHeightAndWidth(){
        var obj = window,
            sname = 'inner';
        if(!('innerWidth' in window)){
            sname = 'client';
            obj = document.documentElement || document.body;
        }
        return {
            width : obj[sname+'Width'],
            height : obj[sname+'Height']
        };
    }
## 8、array-like object 在jquery中的使用方法。如果需要让一个object在console.log时看起来像一个数组

    //The object has to have length and splice
    var x = {length:2, '0':'foo', '1':'bar', splice:function(){}};
    jQuery.fn.init.prototype.splice = [].splice;

## 9、摇一摇时禁止Iphone在页面提示“撤销键入”

    $(window).off('devicemotion').on('devicemotion',function(eventData){
        //dosomething(eventData.accelerationIncludingGravity);
        eventData.preventDefault(); //就是这句了
    });

## 10、禁止页面被拖动

    //去掉IOS滚动到顶部回弹一下的效果
    <meta name="apple-mobile-web-app-capable" content="yes" />
    //这里是列表可以滚动
    $('#J_userchat-list').on('touchmove', function(event){
        event.stopPropagation();
    });
    //全局阻止移动，防止页面被拽
    $(document).on('touchmove', function(event){
        //$('#J_page-box-1').prepend('<div>move body</div>');
        event.preventDefault();
    });

