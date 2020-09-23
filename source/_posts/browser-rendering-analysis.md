---
title: 浏览器渲染剖析[转]
date: 2015-08-05 12:56:00
updated: 2015-08-05 13:07:49
tags: 
- segmentfault
- nrm
- npm
categories: 
- css

---

### 浏览器的渲染过程

#### 浏览器的渲染是通过其渲染引擎实现的

#### 什么是渲染引擎？
渲染引擎是一种对HTML文档进行解析并将其显示在页面上的工具，在浏览器中担任重要任务。

#### 浏览器不同，渲染引擎也不同
 
 - Chrome、Safari、Opera浏览器中使用WebKit引擎
 - Firefox浏览器中使用Gecko引擎
 - IE浏览器中使用Trident引擎

#### 浏览器渲染主流程

 1. 渲染引擎首先解析HTML文档，构建DOM树。同时根据外部CSS文件或页面中的style元素中的内容解析样式信息。
 2. 根据DOM树与样式信息构建渲染树。
 3. 当渲染树被构建完毕时，开始布局(layout)过程，根据渲染树中的位置信息在屏幕中布局元素（即将元素安排在指定位置）。
 4. 接下来开始绘制(paint)过程，将渲染树中的可视化信息绘制在屏幕中。


<!--more-->


这是一个渐进的过程。为达到更好的用户体验，渲染引擎会力求尽快将内容显示在屏幕上。它不必等到整个 HTML 文档解析完毕之后，就会开始构建呈现树和设置布局。在不断接收和处理来自网络的其余内容的同时，呈现引擎会将部分内容解析并显示出来。 这也就是我们为什么将CSS文件在html头中引入的原因。

#### 渲染过程中layout和paint的区别

- layout
  当窗口尺寸被修改（resize)，发生滚动操作，或position、display、width、height等与元素位置相关的样式属性值被更新时触发布局过程。在布局过程中由于要计算所有元素的位置信息，更加会降低页面加载性能。在WebKit引擎中把布局过程称为Layout过程，在Gecko引擎中把绘制过程称为Reflow过程。
- paint
  当color、background-color、visiblity、outline等与视觉相关的样式属性值被更新时触发绘制过程。在绘制过程中由于要重计算元素的视觉信息，所以会降低页面加载性能。在WebKit引擎中把绘制过程称为Painting过程，在Gecko引擎中把绘制过程称为Repaint过程。

#### 触发layout和paint的原因
    
- 元素的追加、修改与删除
- 动画
- 样式修改
- 元素的class属性值修改
- hover伪类选择器所触发的元素状态改变
- 由用户在input元素中的输入而引起的文字节点改变
- 使用offsetWidth、offsetHeight或getComputedStyle取得样式属性值
- 文字字体的改变
- 窗口尺寸的改变(resize)
- 元素透明度的改变
- 页面或元素内的滚动                                              

#### 如何减少layout和paint

- Javascript
  - 通过DocumentFragment减少DOM操作时引起的布局次数
  - 通过复制减少DOM操作时引起的布局次数
  - 使用class汇总多个样式修改
  - 设置动画帧率在60FPS以内
- CSS
  - 使用position:fixed固定布局对性能产生负面影响
  - 对执行动画的元素使用position:absolute可以缩小布局范围
  - overflow:scroll所引起的滚动将对页面性能产生较大影响
  - 使得布局过程或绘制过程消耗较多资源成本的样式属性
        @font-face
        animation
        transition
        box-shadow
        border-radius
        gradient
        opacity
        background-size
        text-align

#### 如何监测layout和paint
   使用chrome dev tools的timeline panel监测layout和paint，从而有针对性的优化页面
   

#### 扩展阅读
[CSS Triger][4]
[Pixels are expensive][5]
[the runtime performance checklist][6]
[浏览器是如何工作的?][1]
[使用dev tools timeline panel][3]

[1]:http://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/
[2]:http://www.kazaff.me/2014/01/18/%E4%BA%86%E8%A7%A3%E6%B5%8F%E8%A7%88%E5%99%A8%E7%9A%84%E6%B8%B2%E6%9F%93%E8%BF%87%E7%A8%8B%E8%BD%AC/
[3]:http://www.kazaff.me/2014/01/18/chrome-developer-tools%E4%B9%8Btimeline%E9%9D%A2%E6%9D%BF/
[4]:http://csstriggers.com/
[5]:http://aerotwist.com/blog/pixels-are-expensive/
[6]:http://calendar.perfplanet.com/2013/the-runtime-performance-checklist/
[7]:http://www.slideshare.net/matenadasdi1/google-chrome-developer-tools-16917759
[8]:http://blog.csdn.net/jaylinzhou/article/details/8579218
[9]:http://tech.uc.cn/?p=2763

**该PPT已经移到 https://github.com/hjzheng/CUF_PPTs**