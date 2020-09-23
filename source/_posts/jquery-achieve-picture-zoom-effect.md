---
title: Jquery实现的图片放大效果
date: 2015-08-11 10:42:00
updated: 2015-08-11 18:00:36
tags: 
- php
- tipi
categories: 
- php

---
自己动手试下吧。


<!--more-->


**html**:

    <!DOCTYPE html>
        <html lang="en">
        
        <head>
            <meta charset="UTF-8">
            <title>pure-css3-loaded-loader-pseudo-class-rotating-animation</title>
            <style type="text/css">
            img {
                height: auto;
                max-width: 100%;
            }
            
            img.expanded {
                max-width: none;
            }
            
            img {
                height: auto;
                max-width: 640px;
                -moz-transition: all 1s;
                -ms-transition: all 1s;
                -o-transition: all 1s;
                -webkit-transition: all 1s;
                transition: all 1s;
            }
            
            img.expanded {
                max-width: 2000px;
            }
            </style>
            <script src="http://code.jquery.com/jquery-1.9.1.min.js"></script>
            <script type="text/javascript">
            $("document").ready(function() {
                var imageLinks = $('a[href$=".png"], a[href$=".jpg"], a[href$=".gif"], a[href$=".bmp"]');
                if (imageLinks.children('img').length) {
                    imageLinks.children('img').each(function() {
                        var currentTitle = $(this).attr('title');
                        $(this).attr('title', currentTitle + ' (click to enlarge image)');
                    });
                    imageLinks.click(function(e) {
                        e.preventDefault();
                        $(this).children('img').toggleClass('expanded');
                    });
                }
            });
            </script>
        </head>
        
        <body>
        <a href="image.png"><img src="http://www.news.cn/photo/titlepic/111615/1116159726_1438819852481_title0h.jpg" alt="smile" id="imgSmile" /></a>
            
        <a href="javascript:void(0)" onclick="toggleExpand()">Click here to enlarge all images.</a>
        <script type="text/javascript">
        function toggleExpand() {
            var expanded = true;
            $('img').each(function() {
                if (!$(this).hasClass('expanded')) {
                    expanded = false;
                    return false;
                }
            });
            $('a[href$=".png"], a[href$=".jpg"], a[href$=".gif"], a[href$=".bmp"]').children('img').each(function() {
                if (expanded)
                    $(this).removeClass('expanded');
                else
                    $(this).addClass('expanded');
            });
        }
        </script>
        </body>
        
        </html>

