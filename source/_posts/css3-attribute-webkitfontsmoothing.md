---
title:  css3属性-webkit-font-smoothing
date: 2015-08-14 09:26:00
updated: 2015-08-14 09:32:08
tags: 
- golang
- func
- 方法
categories: 
- go

---
![BlueprintResponsiveIconGrid.png][1]
CSS3里面加入了一个“-webkit-font-smoothing”属性。

这个属性可以使页面上的字体抗锯齿,使用后字体看起来会更清晰舒服。

加上之后就顿时感觉页面小清晰了。 淘宝也在用哦！

它有三个属性： 

    none                                     ------ 对低像素的文本比较好 
    
    subpixel-antialiased                ------默认值 
    
    antialiased                             ------抗锯齿很好 

例子： 


<!--more-->


    body{ 
    
    -webkit-font-smoothing: antialiased; 
    
    } 

国外某网站例子

附：

`-moz-osx-font-smoothing: inherit | grayscale`

这个属性也是更清晰的作用，特别是图标文字流行的今天。


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/3694365105.png