---
title: "js填写银行卡号，每隔4位数字加一个空格"
categories: [ "JS" ]
tags: [ "javascript","bankcard" ]
draft: false
slug: "js-fill-in-the-bank-card-number-every-4-digits-plus-a-space"
date: "2011-07-10 17:09:00"
---

*js:*

    !function () {
        document.getElementById('bankCard').onkeyup = function (event) {
            var v = this.value;
            if(/\S{5}/.test(v)){
                this.value = v.replace(/\s/g, '').replace(/(.{4})/g, "$1 ");
            }
        };
    }();


<!--more-->


*javascript:*

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <body>
    <input type="text" id="J_BankCard"/>
    <script src="http://static.ydcss.com/libs/jquery/1.11.2/jquery.js"></script>
    <script>
        !function () {
            $('#J_BankCard').on('keyup mouseout input',function(){
                var $this = $(this),
                    v = $this.val();
                /\S{5}/.test(v) && $this.val(v.replace(/\s/g,'').replace(/(.{4})/g, "$1 "));
            });
        }();
    </script>
    </body>
    </html>