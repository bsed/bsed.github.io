---
title: 网页在Safari快速滚动和回弹的原理
date: 2013-11-02 21:56:00
updated: 2015-11-07 21:57:29
tags: 
- js
- url
categories: 
- js

---
现在很多for Mobile的HTML5网页内都有快速滚动和回弹的效果，看上去和原生app的效率都有得一拼。要实现这个效果很简单，只需要加一行css代码即可：

    .container{-webkit-overflow-scrolling : touch;}

可用以下网页测试：

    <!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>scroll</title>
            <style type="text/css">
                .container {
                    width : 300px;
                    height : 50%;
                    -webkit-box-sizing : border-box;
                    position : relative;
                    overflow-y : auto;
                    background-color : cyan;
                    -webkit-overflow-scrolling : touch;  
                    /* liuhx:可以把这整行注释掉对比差别 */


<!--more-->


                }
                ul {
                    height: 50px;
                }
            </style>
        </head>
    <body>
        <div align="center">
            <nav class="container">
                <ul>1</ul>
                <ul>2</ul>
                <ul>3</ul>
                <ul>4</ul>
                <ul>5</ul>
                <ul>6</ul>
                <ul>7</ul>
                <ul>8</ul>
                <ul>9</ul>
                <ul>10</ul>
                <ul>11</ul>
                <ul>12</ul>
                <ul>13</ul>
                <ul>14</ul>
                <ul>15</ul>
                <ul>16</ul>
                <ul>17</ul>
                <ul>18</ul>
                <ul>19</ul>
                <ul>20</ul>
            </nav>
        </div>
    </body>
    </html>