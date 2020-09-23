---
title: jQuery的函数遍历
date: 2013-06-10 16:22:00
updated: 2015-07-27 16:26:51
tags: 
- life
categories: 
- default

---
jQuery 遍历函数包括了用于筛选、查找和串联元素的方法。

函数 描述

    .add() 将元素添加到匹配元素的集合中。
    .andSelf() 把堆栈中之前的元素集添加到当前集合中。
    .children() 获得匹配元素集合中每个元素的所有子元素。
    .closest() 从元素本身开始，逐级向上级元素匹配，并返回最先匹配的祖先元素。
    .contents() 获得匹配元素集合中每个元素的子元素，包括文本和注释节点。
    .each() 对 jQuery 对象进行迭代，为每个匹配元素执行函数。
    .end() 结束当前链中最近的一次筛选操作，并将匹配元素集合返回到前一次的状态。
    .eq() 将匹配元素集合缩减为位于指定索引的新元素。
    .filter() 将匹配元素集合缩减为匹配选择器或匹配函数返回值的新元素。
    .find() 获得当前匹配元素集合中每个元素的后代，由选择器进行筛选。
    .first() 将匹配元素集合缩减为集合中的第一个元素。
    .has() 将匹配元素集合缩减为包含特定元素的后代的集合。
    .is() 根据选择器检查当前匹配元素集合，如果存在至少一个匹配元素，则返回 true。
    .last() 将匹配元素集合缩减为集合中的最后一个元素。
    .map() 把当前匹配集合中的每个元素传递给函数，产生包含返回值的新 jQuery 对象。
    .next() 获得匹配元素集合中每个元素紧邻的同辈元素。
    .nextAll() 获得匹配元素集合中每个元素之后的所有同辈元素，由选择器进行筛选（可选）。
    .nextUntil() 获得每个元素之后所有的同辈元素，直到遇到匹配选择器的元素为止。
    .not() 从匹配元素集合中删除元素。
    .offsetParent() 获得用于定位的第一个父元素。
    .parent() 获得当前匹配元素集合中每个元素的父元素，由选择器筛选（可选）。
    .parents() 获得当前匹配元素集合中每个元素的祖先元素，由选择器筛选（可选）。
    .parentsUntil() 获得当前匹配元素集合中每个元素的祖先元素，直到遇到匹配选择器的元素为止。
    .prev() 获得匹配元素集合中每个元素紧邻的前一个同辈元素，由选择器筛选（可选）。
    .prevAll() 获得匹配元素集合中每个元素之前的所有同辈元素，由选择器进行筛选（可选）。
    .prevUntil() 获得每个元素之前所有的同辈元素，直到遇到匹配选择器的元素为止。
    .siblings() 获得匹配元素集合中所有元素的同辈元素，由选择器筛选（可选）。
    .slice() 将匹配元素集合缩减为指定范围的子集。

**each的用法**


<!--more-->


## 1.数组中的each

     var arr = [ "one", "two", "three", "four"];   
     $.each(arr, function(){   
      alert(this);   
     });  
    //上面这个each输出的结果分别为：one,two,three,four  
      
    var arr1 = [[1, 4, 3], [4, 6, 6], [7, 20, 9]]   
    $.each(arr1, function(i, item){   
      alert(item[0]);   
    });   
    //其实arr1为一个二维数组，item相当于取每一个一维数组，  
    //item[0]相对于取每一个一维数组里的第一个值  
    //所以上面这个each输出分别为：1  4  7   
     
     
    var obj = { one:1, two:2, three:3, four:4};   
    $.each(obj, function(i) {   
      alert(obj[i]);      
    });  
    //这个each就有更厉害了，能循环每一个属性   
    //输出结果为：1  2 3 4 

## 2.遍历Dom元素中

    <html>
    <head>
    <script type="text/javascript" src="/jquery/jquery.js"></script>
    <script type="text/javascript">
    $(document).ready(function(){
     $("button").click(function(){
      $("li").each(function(){
       alert($(this).text())
      });
     });
    });
    </script>
    </head>
    <body>
    <button>输出每个列表项的值</button>
    <ul>
    <li>Coffee</li>
    <li>Milk</li>
    <li>Soda</li>
    </ul>
    </body>
    </html>

依次弹出Coffee，Milk，Soda

## 3.each和map的比较

下面的例子是获取每一个多框的ID值；

### each方法：

定义一个空数组，通过each方法，往数组添加ID值；最后将数组转换成字符串后，alert这个值；

    $(function(){
      var arr = [];
      $(":checkbox").each(function(index){
        arr.push(this.id);
      });
      var str = arr.join(",");
      alert(str);
    })

### map方法:

将每个:checkbox执行return this.id；并将这些返回值，自动的保存为jQuery对象，然后用get方法将其转换成原生javascript数组，再使用join方法转换成字符串，最后alert这个值；

    $(function(){
      var str = $(":checkbox").map(function() {
        return this.id;
      }).get().join();  
      alert(str);
    })

当有需一个数组的值的时候，用map方法，很方便。

### 4.jquery中使用each

例遍数组，同时使用元素索引和内容。（i是索引，n是内容）

代码如下:

    $.each( [0,1,2], function(i, n){
    alert( "Item #" + i + ": " + n );
    }); 

例遍对象，同时使用成员名称和变量内容。（i是成员名称，n是变量内容）

代码如下:

    $.each( { name: "John", lang: "JS" }, function(i, n){
    alert( "Name: " + i + ", Value: " + n );
    }); 

例遍dom元素，此处以一个input表单元素作为例子。

如果你dom中有一段这样的代码

    <input name="aaa" type="hidden" value="111" /> 
    <input name="bbb" type="hidden" value="222" /> 
    <input name="ccc" type="hidden" value="333" /> 
    <input name="ddd" type="hidden" value="444"/> 

然后你使用each如下

代码如下:

    $.each($("input:hidden"), function(i,val){
    alert(val); //输出[object HTMLInputElement]，因为它是一个表单元素。
    alert(i); //输出索引为0，1，2，3
    alert(val.name); //输出name的值
    alert(val.value); //输出value的值
    }); 

## 5.each中根据this查找元素

实现效果”回复”两个字只有在鼠标经过的时候才显示出来

    <ol class="commentlist">
      <li class="comment">
        <div class="comment-body">
         <p>嗨，第一层评论</p>
         <div class="reply">
          <a href="#" class=".comment-reply-link">回复</a>
         </div>
        </div>
        <ul class="children">
         <li class="comment">
          <div class="comment-body">
          <p>第二层评论</p>
          <div class="reply">
           <a href="#" class=".comment-reply-link">回复</a>
          </div>
         </div></li>
        </ul>
      </li>
    </ol>

JS代码如下

    $("div.reply").hover(function(){
     $(this).find(".comment-reply-link").show();
    },function(){
     $(this).find(".comment-reply-link").hide();
    });

实现效果，验证判断题是否都有选择

html

    <ul id="ulSingle">
      
          <li class="liStyle">
            1. 阿斯顿按时<label id="selectTips" style="display: none" class="fillTims">请选择</label>
            <!--begin选项-->
            <ul>
              
                  <li class="liStyle2">
                    <span id="repSingle_repSingleChoices_0_labOption_0">A     </span>.阿萨德发<input type="hidden" name="repSingle$ctl00$repSingleChoices$ctl00$hidID" id="repSingle_repSingleChoices_0_hidID_0" value="1" />
                    <input id="repSingle_repSingleChoices_0_cheSingleChoice_0" type="checkbox" name="repSingle$ctl00$repSingleChoices$ctl00$cheSingleChoice" /></li>
                
                  <li class="liStyle2">
                    <span id="repSingle_repSingleChoices_0_labOption_1">B     </span>.阿萨德发<input type="hidden" name="repSingle$ctl00$repSingleChoices$ctl01$hidID" id="repSingle_repSingleChoices_0_hidID_1" value="2" />
                    <input id="repSingle_repSingleChoices_0_cheSingleChoice_1" type="checkbox" name="repSingle$ctl00$repSingleChoices$ctl01$cheSingleChoice" /></li>
                
                  <li class="liStyle2">
                    <span id="repSingle_repSingleChoices_0_labOption_2">C     </span>.阿斯顿<input type="hidden" name="repSingle$ctl00$repSingleChoices$ctl02$hidID" id="repSingle_repSingleChoices_0_hidID_2" value="3" />
                    <input id="repSingle_repSingleChoices_0_cheSingleChoice_2" type="checkbox" name="repSingle$ctl00$repSingleChoices$ctl02$cheSingleChoice" /></li>
                
            </ul>
            <!--end选项-->
            <br />
          </li>
        
    </ul>

js代码

    //验证单选题是否选中
        $("ul#ulSingle>li.liStyle").each(function (index) {
          //选项个数
          var count = $(this).find("ul>li>:checkbox").length;
          var selectedCount = 0
          for (var i = 0; i < count; i++) {
            if ($(this).find("ul>li>:checkbox:eq(" + i + ")").attr("checked")) {
              selectedCount++;
              break;
            }
          }
          if (selectedCount == 0) {
            $(this).find("label#selectTips").show();
            return false;
          }
          else {
            $(this).find("label#selectTips").hide();
          }
        })


## 6.官方解释

以下是官方的解释：

`jQuery.each(object, [callback])`

### 概述

通用例遍方法，可用于例遍对象和数组。

不同于例遍 jQuery 对象的 $().each() 方法，此方法可用于例遍任何对象。回调函数拥有两个参数：第一个为对象的成员或数组的索引，第二个为对应变量或内容。如果需要退出 each 循环可使回调函数返回 false，其它返回值将被忽略。

### 参数

`objectObject`

需要例遍的对象或数组。

`callback (可选)Function`

每个成员/元素执行的回调函数。

