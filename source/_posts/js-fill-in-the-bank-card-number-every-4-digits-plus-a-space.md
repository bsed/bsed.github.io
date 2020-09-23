---
title: js填写银行卡号，每隔4位数字加一个空格
date: 2011-07-10 17:09:00
updated: 2015-11-07 17:11:17
tags: 
- life
categories: 
- default

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