---
title: vscode下vue多属性换行设置
date: 2018-07-20 01:20:00
updated: 2018-12-27 08:52:23
tags: 
- github
- git
- gitlab
categories: 
- linux

---
如何在vscode 下格式化 vue 代码，让其看起来更加漂亮呢？
在vue的官方文档中,有这么个最佳实践:
![vue-multi-format.png][1]
示例如下:

**Bad:**
```javascript:
<img src="https://tygasoft.com/usr/themes/ob/img/logo2.svg" alt="Vue Logo">
<MyComponent foo="aa" bar="bb" baz="cc"/>
```
**Good:**
```javascript
<img
  src="https://tygasoft.com/usr/themes/ob/img/logo2.svg"
  alt="Vue Logo"
>
<MyComponent
  foo="aa"
  bar="bb"
  baz="cc"
/>
```
但是在vscode中,进行格式化,却得不到官方指导的效果,经过多方查找,终于找到解决方案.

[github issue#561](https://github.com/vuejs/vetur/issues/561)


<!--more-->


具体配置如下:
```json
"vetur.format.defaultFormatter.html": "js-beautify-html",
"vetur.format.defaultFormatterOptions": {
    "js-beautify-html": {
      "wrap_attributes": "force-aligned"
    }
},
```
ok,大功告成


  [1]: https://imgs.gnux.cn/usr/uploads/2018/07/2048685262.png