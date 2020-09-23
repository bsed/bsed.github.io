---
title: D3(Data-Driven Documents)
date: 2015-08-11 08:50:00
updated: 2015-09-06 22:06:51
tags: 
- jquery
- getjson
categories: 
- js

---
### 什么是D3?

这里的D3,首先不是尼康相机也不是破坏神3更不是维生素。

摘自[数据可视化实战：使用D3设计交互式图表](http://www.duokan.com/book/71236)
> D3（有时候也叫D3或d3.js）是一个JavaScript库，用于创建数据可视化图形。
> 事实上，D3是一个缩写，它的全称叫Data-Driven Documents（数据驱动的文档）。
> 数据来源于你，而文档就是基于Web的文档（或者网页），代表可以在浏览器中展现的一切，
> 比如HTML、SVG。D3扮演的是一个驱动程序的角色，因为它联系着数据和文档。


<!--more-->


### D3优点

- 资料丰富，案例非常多。
- SVG矢量图形的特点是无损缩放，这个优势在显示2D图形式会有非常好的效果，并且兼容各种分辨率。
- SVG图形的节点可以像dom元素一样控制，这就让自主创作图形变得更容易。相对于canvas这也是非常大的优势。

### D3缺点

- SVG是2D矢量图，不能画3D图形。（用2D矢量可以画很多带透视效果的伪3D图，那并不是真正的3D图！）
d3.
- 不支持IE6，7，8。如果想要IE8使用d3，请用r2d3.js（一个结合了 Raphael.js的扩展库）。Raphael.js是一个跨浏览器的矢量图形库，它实现IE6，7，8兼容的方法是：在IE6，7，8中使用VML，在其他浏览器中使用SVG。另外，如果图形复杂，就不要指望用Raphael.js在IE上能跟D3画出一样酷炫的效果。
- SVG的节点都是对象，非常占用内存。例如论坛里一个朋友使用d3绘制超过12000个节点的图，直接导致每个试图打开它的浏览器都崩溃了。这个时候如果不愿意做简化那么应该试试canvas绘图。


### 一个例子基于AngularJS (http://projects.delimited.io/experiments/last-fm/)
```javascript 
angular.module('viz').directive('toptagChart', ['lastfm', 

  function (lastfm) {

    var link = function ($scope, $el, $attrs) {
      //定义大小
      var diameter = 500;
      
      //使用pack布局,关于pack你可以查看D3 API
      var bubble = d3.layout.pack()
        .sort(null)
        .size([diameter, diameter])
        .padding(2.5);
      
      //创建svg元素,并定义大小 
      var svg = d3.select($el[0]).append("svg")
        .attr({width: diameter, height: diameter})
        .attr("viewBox", "0 0 " + diameter + " " + diameter);

      //追加g元素 
      var chart = svg.append("g");

      //追加text元素,显示loading
      chart.append("text").attr("id", "loading")
        .text("Loading...")
        .attr("transform", "translate(200,250)");

      var update = function () {
        var data = $scope.toptags.map(function (d) {
          d.value = d[$scope.tagsize];
          return d;
        });

		//将数据塞入pack布局中,pack布局会
		//自动帮我们计算坐标和bubble的半径大小根据value
        bubble.nodes({children: data});

        //data如果有数据,删除text元素
        if (data.length) chart.select("#loading").remove();

		//选取所有class为node元素
		//很多人很奇怪，为啥元素没有创建，却要去选取
		//这就是D3的特点,通过下面的enter()方法先占位
		//enter(), 这个方法会分析当前选择的DOM 元素和传给它的数据，
		//如果数据值比对应的DOM 元素多，就创建一个新的占位元素。
		//然后把这个新占位元素的引用交给链中的下一个方法
        var selection = chart.selectAll(".node")
          .data(data);

        //enter方法占位,追加g元素，并添加node class
        var enter = selection.enter()
          .append("g").attr("class", "node")
          .attr("transform", function (d) { 
            return "translate(" + d.x + "," + d.y + ")"; 
          });

		//在g元素里面添加圆,利用pack布局计算好的圆心坐标和半径
        enter.append("circle")
          .attr("r", function (d) { return d.r; })
          .style("fill", '#FFCB72')
          .on("click", function (d) {
            svg.selectAll("circle").style("fill", '#FFCB72');
            d3.select(this).style("fill", "#19314A");

            lastfm.topArtists(d.name)
              .success(function (res) {
                if (res.error) {
                  throw new Error(res.message);
                } else {
                 $scope.currtag = d.name;
                  var artists = res.topartists.artist.map(function (a) {
                    a.genre = d.name;
                    a.arank = +a['@attr'].rank;
                    return a;
                  });
                  $scope.artists = artists;
                }
              });
          });

		//在g元素里追加文本
        enter.append("text")
          .attr("dy", ".3em")
          .style("text-anchor", "middle")
          .text(function (d) { return d.name; });

		//当圆心坐标改变时，增加translation效果
        selection.transition().duration(2000)
          .attr("transform", function (d) { 
            return "translate(" + d.x + "," + d.y + ")";
          });

		//当圆的半径改变时，增加translation效果
        selection.selectAll("circle").transition().duration(3000)
          .attr("r", function (d) { return d.r; });

        resize();
      };

      function resize() {
        svg.attr("width", $el[0].clientWidth);
        svg.attr("height", $el[0].clientWidth); //It's a square
      }

      $scope.$on('windowResize',resize);
      //tagsize如果有变化,调用update方法
      $scope.$watch('tagsize', update);
      $scope.$watch('toptags', update);

    };
    return {
      template: '<div class="chart col-sm-12 col-md-12 col-lg-12 col-xl-12"></div>',
      replace: true,
      link: link, 
      restrict: 'E' 
    };
}]);
```
*模型更新例子：*

    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="content-type" content="text/html; charset=UTF-8">
        <title>Test of D3.js</title>
        <meta charset="utf-8">
        <!-- 调用d3.js库 -->
        <script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
        <style type="text/css">
    
        text {
          font: bold 48px monospace;
          align: center;
        }
    
        .enter {
          fill: green;
          align: center;
        }
    
        .update {
          fill: #333;
          align: center;
        }
    
        </style>
    </head>
    <body>
        <script>
    
        //创建字符串并将一个字符串分割成字符串数组
        var alphabet = "abcdefghijklmnopqrstuvwxyz".split("");
    
        //定义宽度和高度
        var width = 960,
            height = 500;
    
        //基于svg画布新建元素g
        var svg = d3.select("body").append("svg")
            .attr("width", width)
            .attr("height", height)
          .append("g")
            .attr("transform", "translate(32," + (height / 2) + ")");
    
        function update(data) {
    
          // DATA JOIN
          // 数据绑定
          var text = svg.selectAll("text")
              .data(data);
    
          // UPDATE
          text.attr("class", "update");
    
          // ENTER
          // 对新文字进行元素生成
          // 注意enter()与exit()两个函数的使用方法
          text.enter().append("text")
              .attr("class", "enter")
              .attr("x", function(d, i) { return i * 32; })//文字位置,从添加填补的位置开始
              .attr("dy", ".35em");
    
          // ENTER + UPDATE
          text.text(function(d) { return d; });
    
          // EXIT
          // 删除旧的未使用到的元素
          text.exit().remove();
        }
    
        // 初始化显示模块.
        update(alphabet);
    
        // 以字典序显示一段随机分隔的文字块.
        // setInterval() 方法可按照指定的周期（以毫秒计）来调用函数或计算表达式。它会不停地调用函数，直到 clearInterval() 被调用或窗口被关闭。由 setInterval() 返回的 ID 值可用作 clearInterval() 方法的参数。
        // slice() 方法可提取字符串的某个部分，并以新的字符串返回被提取的部分。
        // Math.random() -- 返回0和1之间的伪随机数
        setInterval(function() {
          update(shuffle(alphabet)
              .slice(0, Math.floor(Math.random() * 26))
              .sort());
        }, 1500);
    
        // 对存在数组进行洗牌
        function shuffle(array) {
          var m = array.length, t, i;
          while (m) {
            i = Math.floor(Math.random() * m--);
            t = array[m], array[m] = array[i], array[i] = t;
          }
          return array;
        }
    
        </script>
    </body>
    </html>


### 另一个例子,是我自己写的(http://get-set.cn/front-end-chart/)


### 参考资料

[我涉及的数据可视化的实现技术和工具](http://www.cnblogs.com/zhangdi/p/3690284.html)
[数据可视化实战：使用D3设计交互式图表](http://www.duokan.com/book/71236)
[Creating Custom D3 Directives in AngularJS](http://www.delimited.io/blog/2014/7/16/d3-directives-in-angularjs)