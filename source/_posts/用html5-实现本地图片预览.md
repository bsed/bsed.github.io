---
title: "用html5 实现本地图片预览"
categories: [ "Css" ]
tags: [ "html5" ]
draft: false
slug: "3153"
date: "2014-07-29 12:09:00"
---

```html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>test</title>
</head>
<body>
    <input type="file" accept="image/*" id="img"/>
    <img id="showImg" alt="待显示的图片"/>
    <script>
        window.onload = function() {
            var fileInput = document.getElementById('img');
            fileInput.addEventListener('change', function(e) {
                // 获取files
                var files = e.target.files;
                // 获取单个file
                if (files && files.length) {
                    var file = files[0];
                    // 通过 FileReader
                    var reader = new FileReader();
                    reader.onload = function(event) {
                        var image = new Image();
                        image.src = event.target.result;
                        image.width = 100;
                        document.body.appendChild(image);
                    };
                    reader.readAsDataURL(file);
                    // ----------------------------------------
                    // 通过 window.URL 工具从 file 对象生成一个可用的 URL
                    var URL = window.URL || window.webkitURL;
                    var imgURL = URL.createObjectURL(file);
                    // 显示
                    document.getElementById('showImg').src = imgURL;
                    setTimeout(function() {
                        // 使用下面这句可以在内存中释放对此 url 的伺服
                        URL.revokeObjectURL(imgURL);
                        // 再次设置无效!
                        document.getElementById('showImg').src = imgURL;
                    }, 1000);
                }
            });
        }
    </script>
</body>
</html>
```