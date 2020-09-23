---
title: 在vue 使用jsPdf 将html 转为 pdf,并且解决图片跨域传输问题
date: 2019-10-15 09:12:00
updated: 2019-10-15 09:23:45
tags: 
- nodejs
- npm
- jspdf
categories: 
- js

---
从其他服务器获取的图片url，生成pdf时，总会出现图片跨域传输问题。 

## 解决方案

在`html2canvas`中，设置属性`useCORS: true`。

<!--more-->

具体代码如下：
```javascript
import html2Canvas from 'html2canvas'
import JsPDF from 'jspdf'

export default{
  install (Vue, options) {
    Vue.prototype.getPdf = function (id,title) {
      html2Canvas(document.querySelector(`#${id}`), {
        // allowTaint: true
        useCORS:true//看情况选用上面还是下面的，
      }).then(function (canvas) {
          console.log(canvas)
            let contentWidth = canvas.width
            let contentHeight = canvas.height
            let pageHeight = contentWidth / 592.28 * 841.89 
            let leftHeight = contentHeight
            let position = 0
            let imgWidth = 595.28  
            let imgHeight = 592.28 / contentWidth * contentHeight 
            let pageData = canvas.toDataURL('image/jpeg', 1.0)
            let PDF = new JsPDF('', 'pt', 'a4')
            if (leftHeight < pageHeight) {
                PDF.addImage(pageData, 'JPEG', 0, 0, imgWidth, imgHeight)
            } else {
            while (leftHeight > 0) {
                  PDF.addImage(pageData, 'JPEG', 0, position, imgWidth, imgHeight)
                  leftHeight -= pageHeight
                  position -= 841.89
                  if (leftHeight > 0) {
                      PDF.addPage()
                  }
              }
            }
            PDF.save(title + '.pdf')
        }
      )
    }
  }
}
```


  [1]: https://imgs.gnux.cn/usr/uploads/2019/10/1578313918.png