---
title: 免插件实现 ABP 等拓展的广告屏蔽和反屏蔽
date: 2016-12-30 08:18:00
updated: 2017-05-20 08:20:05
tags: 
- rust
- tls
categories: 
- default

---
# 前言

不管怎么说，广告依旧是个人站长盈利的重心，但是浏览器的广告屏蔽拓展一刀切的屏蔽所有广告，也真是够了（虽然米饭粑至今仍然没有广告）。因此这里介绍一下如何通过代码的方式时间对广告拓展的饭屏蔽。

不过值得一提的是，Cnbeta 这样广告乱放影响阅读的网站也敢让你放弃屏蔽我也真是醉了，所以这里也会提到如何反屏蔽这个拓展。

# 屏蔽拓展

首先，我们是不可能直接让拓展“失效”的，所以我们可以通过提醒访客关闭针对本域名的广告屏蔽。这样比较善意也有效。

经过我的测试，支持主流的 Adblock 、ABP(Adblock Plus) 和 Ublock。


<!--more-->


**一、将下列代码保存为 jquery.adi.js**

```
/**
 * ==============
 *   Adi.js 
 * ==============
 * 
 * @author: Marius Balaj
 * @website: http://mariusbalaj.com
 * @contact: balajmarius93@gmail.com
 *
 * 
 */


/**
 * Check for jQuery
 */

if (typeof jQuery === 'undefined') {
    throw new Error('Make sure jQuery is included before the jquery.adi.js');
}

;(function($) {

    'use strict';


    var Adi;

    $.adi = function(args) {

        /**
         * Merge defaults with user options
         */

        var options = $.extend({}, Adi.defaults, args);
        return new Adi(options);
    };


    /**
     * Constructor
     */

    Adi = function(args) {

        /**
         * Merge this with user options
         */

        $.extend(this, args);

        if (this._check()) {
            this._init();
            this.active();
        }

        if(!this._check()) {
            this.inactive();
        }
    };

    /**
     * Check for $.adblock
     */

    Adi.prototype._check = function() {
        return $.adblock === undefined
    };

    /**
     * Start plugin  
     */

    Adi.prototype._init = function() {
        this._append();
    };

    /**
     * Set template
     */

    Adi.prototype._setTemplate = function(title, content) {

        return '<div class="jquery-adi">' +
            '<div class="jquery-adi_content">' +
            '<button class="jquery-adi_close"></button>' +
            '<h2>' + title + '</h2>' +
            '<p>' + content + '</p>' +
            '</div>' +
            '</div>';
    };

    /**
     * Append html
     */

    Adi.prototype._append = function(callback) {

        this.$el = $(this._setTemplate(this.title, this.content)).appendTo($(document.body)).addClass(this.theme);
        this._show();
    };

    /**
     * Show modal
     */

    Adi.prototype._show = function() {

        var that = this;

        this.$el.show();
        this._center();
        this._controls();

        this.onOpen(this.$el);
    };


    /**
     * Modal controls
     */

    Adi.prototype._controls = function() {

        var that = this;

        function close() {            
            that.$el.hide();
            that.onClose(that.$el);
        }

        this.$el.on('click', '.jquery-adi_close', close);
        $(document).on('keyup', function(e) {
            if (e.keyCode == 27)
                close();
        });
    };


    /**
     * Center modal
     */

    Adi.prototype._center = function() {
        var $modal = this.$el.find('.jquery-adi_content');
        $modal.css('margin-top', -Math.abs($modal.outerHeight() / 2));
    };

    /**
     * Defaults
     */

    Adi.defaults = {
        title: 'Adblock detected!',
        content: 'We noticed that you may have an Ad Blocker turned on. Please be aware that our site is best experienced with Ad Blockers turned off.',
        theme: 'light',
        onOpen: function() {},
        onClose: function() {},
        active: function() {},
        inactive: function() {}
    };

})(jQuery);

```

**二、将下面的代码保存为 advertisement.js**

```
/**
 * if adblock is active, this file will be blocked
 * so $.adblock is not defined
 */

jQuery.adblock = false;

```

**三、将下列代码保存为 jquery.adi.css**

```
/* Adi.js CSS ====== */

.jquery-adi {
    display: none;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    text-align: center;
    font-family: Helvetica Neue, Helvetica, Arial, sans-serif;
    background: rgba(0, 0, 0, .7);
    z-index: 999999;
}

.jquery-adi_content {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 320px;
    padding: 30px 40px;
    background: #ffffff;
    border-radius: 10px;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
    margin-left: -160px;
    box-sizing: border-box;
}

.jquery-adi_content h2,
.jquery-adi_content p,
.jquery-adi_content button {
    padding: 0;
    margin: 0;
}

.jquery-adi_content button {
    position: absolute;
    right: -15px;
    top: -15px;
    border: 0;
    outline: 0;
    width: 30px;
    height: 30px;
    border-radius: 50%;
    background-color: #d5d5d5;
    background-image: url('../images/icn_close.png');
    background-repeat: no-repeat;
    background-position: 0 0;
    box-shadow: -2px 2px 8px rgba(148, 146, 145, 0.3);
}

.jquery-adi_content h2 {
    color: #e76e65;
    font-size: 22px;
    font-weight: bold;
}

.jquery-adi_content h2:before {
    content: "";
    width: 80px;
    height: 80px;
    background-color: #e76e65;
    background-image: url('../images/icn_danger.png');
    background-position: 0 0;
    background-repeat: no-repeat;
    display: block;
    margin: 0 auto 30px;
    border-radius: 50%;
}

.jquery-adi_content p {
    font-size: 14px;
    color: #989898;
    line-height: 1.4;
    margin: 10px 0;
}


/* Dark Theme ====== */

.jquery-adi.dark .jquery-adi_content {
    background: #1F1F1F;
}

.jquery-adi.dark .jquery-adi_content h2 {
    color: #FF4335;
}

.jquery-adi.dark .jquery-adi_content h2:before {
    background-color: #FF4335;
    background-position: bottom center;
}

.jquery-adi.dark .jquery-adi_content p {
    color: #ffffff;
}

.jquery-adi.dark .jquery-adi_content button {
    background-color: #ffffff;
    background-position: bottom left;
}
```

**四、然后在需要出现的页面引用**

```
<--这个添加在 header 位置-->

<link rel='stylesheet' href='jquery.adi.css' type='text/css' media='all' />

<--这个添加在 footer 位置-->

<script type="text/javascript" src="jquery.adi.js"></script>

<script type="text/javascript" src="advertisement.js"></script>

<script>

$.adi

({

 content: "经营网站老辛苦，广告屏蔽不要一刀切。请支持我们的发展，谢谢",

theme: "dark"

});

</script>
```

**五、其中 css 中的两个图片，请自行下载替代**

`**background-image: url('../images/icn_close.png');** 可以在 https://github.com/balajmarius/adi.js/blob/master/icn_close.png`

`**background-image: url('../images/icn_danger.png');** 可以在 https://github.com/balajmarius/adi.js/blob/master/icn_danger.png `

`来自：https://github.com/balajmarius/adi.js `

# 反屏蔽这货

如果那个广告很烦人的页面也 TM 用了这个东西，咋办呢？ 针对该网站加入如下拦截规则即可：

```
.dark.jquery-adi
```