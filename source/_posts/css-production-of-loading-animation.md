---
title: CSS制作的加载Loading动画
date: 2015-08-04 13:16:00
updated: 2015-08-04 13:30:21
tags: 
- css
- normalize
- koala
categories: 
- css

---
`Spinkit`是一个Loading动画集合，采用了最为先进的CSS3动画效果，插件可以高度自定义动画效果，并且提供多个参数可供选择，自备有个动画效果演示，运行性能流畅，是你Loading动画解决方案的绝佳方法。
SpinKit 用来实现各种动态的 Activity Indicator，用于表示正在加载、正在运行等等状态。

SpinKit 对不不支持CSS3 动画的浏览器（例如 IE9 及以下版本浏览器），你可能需要先检测一下，然后再决定是否使用这个插件，你可能会需要一种图片Loading的解决方案。

# 使用方法

## 常规引入方法


<!--more-->


创建一个HTML文档和一个CSS在你的项目中，然后将spinner插件引入您的项目，注意：一个隐藏`opacity: 0` 或` visibility: hidden`的spinner任然会保持动画的旋转，所以可能会浪费一部分系统的资源，在内容加载完毕以后，建议将spinner设置为display: none或者将动画效果设置为`animation-play-state: paused`节约系统资源。

SpinKit可以通过bower安装到你的项目中：

`$ bower install spinkit`

## SCSS

假如你使用SCSS在你的项目中，你可以通过运行下面的代码将SpinKit引入到你的项目中：

    @import '../bower_components/spinkit/scss/spinners/1-rotating-plane',
            '../bower_components/spinkit/scss/spinners/3-wave';

请注意，你现在需要如果你想支持所有的浏览器在你的项目中使用 [autoprefixer](https://github.com/postcss/autoprefixer) 。如果你编译你的计算机可以使用[grunt-autoprefixer](https://github.com/nDmitry/grunt-autoprefixer)与大口大口咕噜咕噜或如果你使用后 [grunt-autoprefixer](https://github.com/nDmitry/grunt-autoprefixer) 是个不错的选择。

有一些变量可以被重写，如果你使用标准。默认的列：[scss/_variables.scss](https://github.com/tobiasahlin/SpinKit/blob/master/scss/_variables.scss)。

# 旧浏览器的兼容性

用CSS动画在每一个当前的浏览器支持几乎90%的浏览器在使用的今天，对于GIF和JavaScript回退需要迅速减少。如果你还需要支持这些最后的这部分是给你的。

## 备用的回调方法

提供一个后备的动画的一个简单的方法是查看该动画属性的支持，如果它不支持旋转与GIF代替。

    function browserSupportsCSSProperty(propertyName) {
      var elm = document.createElement('div');
      propertyName = propertyName.toLowerCase();
    
      if (elm.style[propertyName] != undefined)
        return true;
    
      var propertyNameCapital = propertyName.charAt(0).toUpperCase() + propertyName.substr(1),
        domPrefixes = 'Webkit Moz ms O'.split(' ');
    
      for (var i = 0; i < domPrefixes.length; i++) {
        if (elm.style[domPrefixes[i] + propertyNameCapital] != undefined)
          return true;
      }
    
      return false;
    }

你可以使用下面的代码，检测你的浏览器对 animation 的兼容性

if (!browserSupportsCSSProperty('animation')) {
  // fallback…
}

github地址：[https://github.com/tobiasahlin/SpinKit](https://github.com/tobiasahlin/SpinKit)

官方网站：[http://tobiasahlin.com/spinkit/](https://github.com/tobiasahlin/SpinKit)
演示demo:[http://tobiasahlin.com/spinkit/]