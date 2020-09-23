---
title: Angular的数据绑定 [Ionic Angularjs 学习笔记]
date: 2015-08-30 20:26:00
updated: 2016-06-02 11:10:08
tags: 
- linux
- command
- bsh
categories: 
- linux

---
## 小试牛刀 Hello Word   

    这里是列表文本Angular创建实时模板来代替视图,而不是将数据合并 进模板之后更新DOM。任何一个独立视图组件中的值都是动态替换的。这个功能可以说是 `AngularJS`中最重要的功能之一,也是让我们只用10行代码,并且在没有任何JavaScirpt的情况下 就可以写出Hello World的关键。

    这里是列表文本要实现这个功能,只要在HTML页面中引用`angular.js`,并在某个DOM元素上明确设置 ng-app属性即可。ng-app属性声明所有被其包含的内容都属于这个AngularJS应用,这也是我们 可以在Web应用中嵌套AngularJS应用的原因。只有被具有ng-app属性的DOM元素包含的元素才 会受AngularJS影响。

    自动数据绑定使我们可以将视图理解为模型状态的映射。当客户端的数据模型发生变化时, 视图就能反映出这些变化,并且不需要写任何自定义的代码,它就可以工作。
在MVC(Model View Controller,模型视图控制器)的世界里,控制器可以不必担心会牵 扯到渲染视图的工作。这样我们就不必再担心如何分离视图和控制器逻辑,并且也可以使测试变 得既简单又令人愉悦。


<!--more-->


`ng-init`的使用：ng-init指令用来在指令被调用时设置内部作用域的初始状态， 使用ng-init指令将内部数据模型对象（$scope）中的name赋上了初值并且绑定到了文本字段上。

*代码片段*

    <html>
    <head>
      <title> 测试页 </title>
      <meta charset="utf-8">
      <script src="http://www.hubwiz.com/scripts/angular.min.js"></script>
    </head>
    <body>
    <div ng-app="" ng-init="name='Hello World'">
        {{ name }}！
    </div>  
    </body>
    </html>

ng-model的使用： 使用ng-model指令将内部数据模型对象（$scope）中的name属性绑定到了文本字段上。
 这就意味着无论在文本输入什么字段，都会同步到数据模型中。

*代码片段*

    <html>
    <head>
      <title> 测试页 </title>
      <meta charset="utf-8">
      <script src="http://www.hubwiz.com/scripts/angular.min.js"></script>
    </head>
    <body>
     <input ng-model='person.name' type="text" placeholder="Your name"/>
              <h1>{{person.name}}</h1>
    </body>
    </html>

## 控制器简单的使用

通过Helle Word的练习和使用进行`angular数据绑定`，相信大家对angular有了基本了解，下面就通过实例对控制器的使用进行了解。
使用`ng-controller`实现Hello Word：需要注意的是必须注册 `ng-app` 要不脚本是不会执行的

    <!DOCTYPE HTML>
    <html ng-app>
    <head>
      <title> 测试页 </title>
      <meta charset="utf-8">
      <script src="http://www.hubwiz.com/scripts/angular.min.js"></script>
    </head>
    <body>
    <div ng-controller="MyController">
          <h1> {{ name }}!</h1>
    </div>  
      <script type="text/javascript" >
        function MyController($scope){
          $scope.name="Hello word!!!";
        };
      </script>
    </body>
    </html>

刷新时间显示案例

    <!DOCTYPE HTML>
    <html ng-app>
    <head>
      <title> 测试页 </title>
      <meta charset="utf-8">
      <script src="http://www.hubwiz.com/scripts/angular.min.js"></script>
    </head>
    <body>
    <div ng-controller="MyController">
          <h1>Hello {{ clock }}!</h1>
    </div>  
      <script type="text/javascript" >
        function MyController($scope,$timeout){
        var updateClock = function() {
                   $scope.clock = new Date();
                   $timeout(function() {
                     updateClock();
                   }, 1000);
    };
                 updateClock();
        };
      </script>
    </body>
    </html>

将上面的数据绑定改为最佳实现：
   由于javaScript自身的特点，以及它在传值和引用时的不同处理方式，通常认为，在视图中通过对象的属性而非对象本身来进行引用绑定,是Angular中的最佳实践。

修改后的格式为最佳实现方式

    <!DOCTYPE HTML>
    <html ng-app>
    <head>
      <title> 测试页 </title>
      <meta charset="utf-8">
      <script src="http://www.hubwiz.com/scripts/angular.min.js"></script>
    </head>
    <body>
    <div ng-controller="MyController">
          <h1>Hello {{ clock.now }}!</h1>
    </div>  
      <script type="text/javascript" >
        function MyController($scope){
                   $scope.clock = {
                   now: new Date();
                   }
              var updateClock=function(){
                   $scope.clock.now=new Date()
                   };
                setInterval(function(){
                      $scope.$apply(updateClock)
                 }, 1000);
                 updateClock();
        };
      </script>
    </body>
    </html>

## Ionic：指令

ionic.js对AngularJS进行了扩展，它的主要贡献是将移动端开发中常见 的UI组件抽象成AngularJS的指令，便于我们可以在HTML开发中快速应用。
图片丢失:>

## ionic：路由管理
在单页应用（Single Page App）中，路由的管理是很重要的环节。ionic.js 没有使用AngularJS内置的ng-route模块，而是选择了AngularUI项目 的ui-router模块。

ui-router的核心理念是将子视图集合抽象为一个状态机，导航意味着 状态的切换。在不同的状态下，ionic.js渲染对应的子视图（动态加载的HTML片段） 就实现了路由导航的功能：

路由管理写法一、

demo.js

在单页应用（Single Page App）中，路由的管理是很重要的环节。ionic.js 没有使用AngularJS内置的ng-route模块，而是选择了AngularUI项目 的ui-router模块。

ui-router的核心理念是将子视图集合抽象为一个状态机，导航意味着 状态的切换。在不同的状态下，ionic.js渲染对应的子视图（动态加载的HTML片段） 就实现了路由导航的功能：

图片丢失:>

路由管理写法一、
demo.js

*代码片段*
    angular.module("ezApp",["ionic"])
    .config(function($stateProvider){
      //配置状态机的各个状态
      $stateProvider
        .state(
    "state1",
    {
    templateUrl:"state1.html"
    })
        .state(
    "state2",
    {
    templateUrl:"state2.html"
    })
        .state(
    "state3",{
    templateUrl:"state3.html"
    });
    })
    .controller("ezCtrl",function($scope,$state){
      //根据参数切换到指定的状态
      $scope.go = function(state){
        $state.go(state);
      };
    });

demo.html
*代码片段*

    <!DOCTYPE html>
    <html ng-app="ezApp">
    <head>
      <meta name="viewport" content="initial-scale=1,maximum-scale=1,user-scalable=no,width=device-width,height=device-height">
      <script src="ionic.bundle.min.js"></script>
      <link rel="stylesheet" type="text/css" href="ionic.min.css">
    </head>
    <body ng-controller="ezCtrl">
      <!--视图显示容器：子视图将在这里显示-->
      <ion-nav-view>
        <img src="img/0019.png">
      </ion-nav-view>
    
      <ion-footer-bar>
        <div class="button-bar">
          <a class="button" ng-click="go('state1');">state1</a>
          <a class="button" ng-click="go('state2');">state2</a>
          <a class="button" ng-click="go('state3');">state3</a>
        </div>
      </ion-footer-bar>
      
      <!--子视图1：state1.html-->
      <script id="state1.html" type="text/ng-template">
        <p>这个内容在状态为state1时显示</p>
        <img src="img/0016.png">
      </script>
    
      <!--子视图2：state2.html-->
      <script id="state2.html" type="text/ng-template">
        <p>这个内容在状态为state2时显示</p>
        <img src="img/0017.png">
      </script>
    
      <!--子视图3：state3.html-->
      <script id="state3.html" type="text/ng-template">
        <p>这个内容在状态为state3时显示</p>
        <img src="img/0018.png">
      </script>
    
    </body>
    </html>

路由管理写法二、

*代码片段*

    angular.module('starter', ['ionic'])
    .config(function($stateProvider, $urlRouterProvider) {
      $stateProvider
    
      // setup an abstract state for the tabs directive
        .state('tab', {
        url: "/tab",
        abstract: true,
        templateUrl: "templates/tabs.html"
      })
    
      // Each tab has its own nav history stack:
    
      .state('tab.dash', {
        url: '/dash',
        views: {
          'tab-dash': {
            templateUrl: 'templates/tab-news.html',
            controller: 'articleController'
          }
        }
      })
    
      .state('tab.chats', {
          url: '/chats',
          views: {
            'tab-chats': {
              templateUrl: 'templates/tab-forum.html',
              controller: 'ChatsCtrl'
            }
          }
        })
        .state('tab.chat-detail', {
          url: '/chats/:chatId',
          views: {
            'tab-chats': {
              templateUrl: 'templates/chat-detail.html',
              controller: 'ChatDetailCtrl'
            }
          }
        })
    
      .state('tab.account', {
        url: '/account',
        views: {
          'tab-account': {
            templateUrl: 'templates/tab-ucenter.html',
            controller: 'AccountCtrl'
          }
        }
      });
    
      // if none of the above states are matched, use this as the fallback
      $urlRouterProvider.otherwise('/tab/dash');
    
    });
