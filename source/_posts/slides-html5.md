---
title: HTML5 必备技能树
date: 2015-08-06 18:05:00
updated: 2015-08-06 18:07:59
tags: 
- css
- 社会化
categories: 
- css

---
关于HTML5, 其实我们一直再说, 从来没有认真过, 下面是Chun Lin Yang同学分享的HTML5 Slides.

#### Offline / Storage

- Web Storage
- Web SQL Database
- IndexedDB
- Application Cache
- Quota API


<!--more-->


#### Realtime / Communication

- Web Workers
- WebSocket
- Notifications(https://developer.mozilla.org/en-US/docs/Web/API/notification) Example: http://jsfiddle.net/clyang/5fwg5unw/4/

#### File / Hardware Access

- Native Drag & Drop
- Desktop Drag-In (File API)
- Desktop Drag-Out
- FileSystem APIs
- Geolocation
- Device Orientation
- Speech Input

#### Semantics & Markup

- Better semantic tags
- Markup for applications
- Descriptive link relations
- Microdata
- ARIA attributes(Role Definitons: http://www.w3.org/TR/wai-aria/roles#role_definitions)
FAQ: https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Web_applications_and_ARIA_FAQ)
- New form types
- Form field types on mobile

#### HTML5 Slides
you can get slides from here
http://slides.html5rocks.com/#landing-slide

#### Graphics / Multimedia
- Audio + Video
- FullScreen API
- Canvas 2D
- Canvas 3D (WebGL)
- Inline SVG

#### CSS3
- New CSS Selector
- Webfonts
- Text wrapping
- Columns
- Text stroke
- Hue/saturation/luminance color
- Rounded corners
- Gradients
- Shadows
- Background enhancements
- Border image
- Flexible Box Model
- Transitions
- Transforms
- Animations

#### Nuts & Bolts
- New Selectors
- Custom data-* attributes
- Element.classList
- History API

### [Learn much javascript from one line of code][7]

#### 代码: 为页面所有元素添加1像素outline
```javascript
[].forEach.call($$("*"),function(a){
  a.style.outline="1px solid #"+(~~(Math.random()*(1<<24))).toString(16)
})
```

你可以在console中运行该code查看效果

#### 分析代码
- $$("*");
- [].forEach.call()
- (~~(Math.random()*(1<<24))).toString(16)

$$("*") 这个来自[command_line_API][0],类似的还有$和$x, 该方法类似于document.querySelectorAll('*')

上述方法得到是nodeList对象,是一个array-like对象,它不是一个数组,没有数组的方法, 所以使用数组的call方法,借用数组的forEach方法, [].forEach.call()

给所有元素加outline,如何取得颜色值? 
1<<24 向左位移
```javascript
1<<0 //2^0
1<<1 //2^1
1<<2 //2^2
1<<3 //2^3
```
Math.random()*(1<<24)取得0到2^24(FFFFFF)的随机数字.

~~ 取整数,类似于parseInt ~按位取反, [更多信息][2] 
```javascript
var a = 12.34, // ~~a = 12
    b = -1231.8754, // ~~b = -1231
    c = 3213.000001; // ~~c = 3213
 
~~a == parseInt(a, 10); // true
~~b == parseInt(b, 10); // true
~~c == parseInt(c, 10); // true
```

toString(16) 转换成16进制
```javascript
(30).toString(); // "30"
(30).toString(10); // "30"
(30).toString(16); // "1e" Hexadecimal
(30).toString(2); // "11110" Binary
(30).toString(36); // "u" 36 is the maximum base allowed
```

[0]:http://getfirebug.com/wiki/index.php/Command_Line_API
[1]:https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence
[2]:http://www.javascriptturnsmeon.com/the-tilde-operator-in-javascript/
[3]:https://github.com/firebug/firebug/blob/master/extension/content/firebug/console/commandLineAPI.js
[4]:https://code.google.com/p/chromium/codesearch#chromium/src/third_party/WebKit/Source/core/inspector/InjectedScriptSource.js
[5]:http://v.youku.com/v_show/id_XODAzOTY1MTM2.html?qq-pf-to=pcqq.group
[6]:http://www.fancynode.com.cn/
[7]:http://arqex.com/939/learning-much-javascript-one-line-code?utm_source=javascriptweekly&utm_medium=email
[8]:http://slides.html5rocks.com/#graphics-multimedia-title

### [JavaScript诞生和死亡 <视频> ] [5]
