---
title: 使用ReactJS + Node实现服务器端响应渲染
date: 2015-01-11 13:04:56
updated: 2015-01-11 13:10:59
tags: 
- orm
categories: 
- sql

---
客户端Javascript驱动的MVC如angular, ember, backbone等的问题是比较慢，用户体验差，同时对搜索引擎不友好，因此，J业界开始实现Javacript的服务器端渲染。

前段时间Ember.js刚推出FastBoot，见这里，并且这几天紧锣密鼓加紧推进中：[INSIDE FASTBOOT: FAKING THE DOM IN NODE.JS](http://emberjs.com/blog/2015/01/08/inside-fastboot-faking-the-dom-in-node.html)。

而同时ReactJS已经推出在Node.js + React 同构渲染的演示：
[Serverside React Rendering: Isomorphic JavaScript](https://reactjsnews.com/isomorphic-javascript-with-react-node/)


<!--more-->


这个案例是一个普通分页批量查询的案例，如下图：
![uploadShowAction.png][1]

点按图中的id或name等表格名，能够实现大小排序，页面下有分页符，通过查看Html源码，表格中数据是以普通html的table格式输出，但是点按分页的下一页，再查看html源码，还是第一页的内容，这就与普通使用Jsp或PHP/ASP等服务器端渲染区别所在，后者每切换一个页面实际是从服务器端再拉取一个新的页面内容，而新式的JS服务器端渲染技术是第一页如同JS/PHP/ASP，一旦输出渲染成功，页面各种效果包括切换到下一页都是由第一页的JS全面掌管，这时如同传统AngularJS等客户端Javascript驱动渲染页面一样，由AngularJS实时修改当前页面的DOM。

这种服务器端和客户端同构Isomorphic JavaScript 技术的好处是：

 1. 更好的用户体验，加载页面快
 2. 搜索引擎友好，利于SEO
 3. 易于维护
 4. 自由逐步增强。

这个代码演示下载后，运行`node server.js`后，浏览器浏览`127.0.0.1:4444`可以得到如上图效果。

看看 server.js主要代码：

    ** @jsx React.DOM */
    
    var React = require('react/addons');
    
    /* create factory with griddle component */
    var Griddle = React.createFactory(require('griddle-react'));
    
    var fakeData = require('../data/fakeData.js').fakeData;
    var columnMeta = require('../data/columnMeta.js').columnMeta;
    var resultsPerPage = 100;
    
    var ReactApp = React.createClass({
    
          componentDidMount: function () {
            console.log(fakeData);
    
          },
    
          render: function () {
    
            return (
              <div id="table-area">
    
                 <Griddle results={fakeData} columnMetadata={columnMeta} resultsPerPage={resultsPerPage} tableClassName="table"/>
    
              </div>
            )
          }
    
      });
    
    /* Module.exports instead of normal dom mounting */
    module.exports.ReactApp = ReactApp;
    /* Normal mounting happens inside of /main.js and is bundled with browerify */

这段代码建立了一个ReactApp模块功能，其功能主要是输出表格内容，内容数据在fakeData.js中，表格表头标题数据在columnMeta.js中。

当前端浏览器访问`127.0.0.1:4444/`时，请求发往端口路由`/app/routes/coreRoutes.js`:

    var React = require('react/addons');
    var ReactApp = React.createFactory(require('../components/ReactApp').ReactApp);
    
    module.exports = function(app) {
    
        app.get('/', function(req, res){
            // React.renderToString 将前面的模块组件ReactApp
            //作为输入，输出产生markup
            var reactHtml = React.renderToString(ReactApp({}));
            // ReactApp输出内容输出到index.ejs的变量reactOutput处
            res.render('index.ejs', {reactOutput: reactHtml});
        });
    
    };

这个路由的功能是直接将ReactApp输出内容输出到index.ejs的变量reactOutput处，index.ejs相当于我们以前的index.htm或index.jsp/index.php等：

    <!doctype html>
    <html>
      <head>
        <title>React Isomorphic Server Side Rendering Example</title>
        <link href='/styles.css' rel="stylesheet">
      </head>
      <body>
        <h1 id="main-title">React Isomorphic Server Side Rendering Example</h1>
        <!-- reactOutput变量输出在这里，它是服务器端React Dom节点-->
        <div id="react-main-mount">
          <%- reactOutput %>
        </div>
    
        <!-- comment out main.js to ONLY see server side rendering -->
        
    
    
      </body>
    </html>

index.ejs中的 <%- reactOutput %>变量类似JSP中的<%=reactOutput %>，这个reactOutput 是模块组件ReactApp({})的输出。

一旦index.ejs输出到浏览器，就形成了上图的效果，然后/main.js应该掌管了整个页面以后的流程，用户在页面上点按排序和分页都是main.js实现即时动态渲染，页面网址不再变化，类似传统的AngularJS效果。

  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/2909435902.png