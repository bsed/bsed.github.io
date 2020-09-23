---
title: Jcrop+uploadify头像裁剪功能
date: 2014-12-06 22:16:00
updated: 2014-12-06 22:19:11
tags: 
- http
- reduce
categories: 
- css

---
利用Jcrop+uploadify实现一个头像在线裁剪之后保存的功能，后端使用play！framework1.附上一些核心代码。
技术栈
[Jcrop](http://deepliquid.com/content/Jcrop_Download.html)
[uploadify](http://www.uploadify.com/download/)
[play！framework 1.2.7](http://downloads.typesafe.com/play/1.2.7/play-1.2.7.zip)(翻墙下载)


<!--more-->


index.html

    <!DOCTYPE html>
    <html>
    <head>
        <title>头像剪裁</title>
        <meta charset="UTF-8">
        <link rel="stylesheet" media="screen" href="@{'/public/stylesheets/jquery.Jcrop.min.css'}">
        <link rel="stylesheet" media="screen" href="@{'/public/stylesheets/uploadify.css'}">
        <script src="@{'/public/javascripts/jquery-1.6.4.min.js'}" type="text/javascript"></script>
        <script src="@{'/public/javascripts/jquery.color.js'}" type="text/javascript"></script>
        <script src="@{'/public/javascripts/jquery.Jcrop.min.js'}" type="text/javascript"></script>
        <script src="@{'/public/javascripts/jquery.uploadify.min.js'}" type="text/javascript"></script>
        <script src="@{'/public/javascripts/script.js'}" type="text/javascript" charset="${_response_encoding}"></script>
    </head>
    <body>
    <div>
        <form id="upload_form" enctype="multipart/form-data" method="post" action="@{Application.cutImage()}">
            <input type="hidden" id="x" name="x"/>
            <input type="hidden" id="y" name="y"/>
            <input type="hidden" id="h" name="h"/>
            <input type="hidden" id="w" name="w"/>
            <img id="preview">
            <input type="file" name="bigImage" id="image_file" onchange="fileSelectHandler()"/>
            <input type="submit" value="上传"/>
        </form>
    </div>
    </body>
    </html>

核心的js代码，下面的id可能需要对应的修改下
script.js

    var jcrop_api, boundx, boundy;
    function updateInfo(e) {
        $('#x').val(e.x);
        $('#y').val(e.y);
        $('#w').val(e.w);
        $('#h').val(e.h);
    };
    function clearInfo() {
        $('.info #w').val('');
        $('.info #h').val('');
    };
    function fileSelectHandler() {
        var oFile = $('#image_file')[0].files[0];
        $('.error').hide();
        var rFilter = /^(image\/jpeg|image\/png)$/i;
        if (! rFilter.test(oFile.type)) {
            alert("请选择正确的文件格式。");
            return;
        }
        if (oFile.size > 500 * 1024) {
            alert("文件太大。");
            return;
        }
        var oImage = document.getElementById('preview');
        var oReader = new FileReader();
            oReader.onload = function(e) {
            oImage.src = e.target.result;
            oImage.onload = function () { // onload event handler
                if (typeof jcrop_api != 'undefined') {
                    jcrop_api.destroy();
                    jcrop_api = null;
                    $('#preview').width(oImage.naturalWidth);
                    $('#preview').height(oImage.naturalHeight);
                }
                $('#preview').Jcrop({
                    minSize: [32, 32], // min crop size
                    aspectRatio : 1, // keep aspect ratio 1:1
                    bgFade: true, // use fade effect
                    bgOpacity: .3, // fade opacity
                    onChange: updateInfo,
                    onSelect: updateInfo,
                    onRelease: clearInfo
                }, function(){
                    var bounds = this.getBounds();
                    boundx = bounds[0];
                    boundy = bounds[1];
                    jcrop_api = this;
                });
            };
        };
        oReader.readAsDataURL(oFile);
    }

图片源作为参数到了后端之后通过下面的java工具类对图片进行剪裁。
ImageCut.java

    public class ImageCut {
        /**
         * 保存图片
         *
         * @param img    原图路径
         * @param destPath   保存的路径
         * @param top    选择框的左边y坐标
         * @param left   选择框的左边x坐标
         * @param width  选择框宽度
         * @param height 选择框高度
         * @return
         * @throws IOException
         */
        public static void saveImage(File img,
                                     String destPath,
                                     int top,
                                     int left,
                                     int width,
                                     int height) throws IOException {
            BufferedImage bi = ImageIO.read(img);
            height = Math.min(height, bi.getHeight());
            width = Math.min(width, bi.getWidth());
            if (height <= 0) {
                height = bi.getHeight();
            }
            if (width <= 0) {
                width = bi.getWidth();
            }
            top = Math.min(Math.max(0, top), bi.getHeight() - height);
            left = Math.min(Math.max(0, left), bi.getWidth() - width);
    
            BufferedImage bi_cropper = bi.getSubimage(top, left, width, height);
            ImageIO.write(bi_cropper, "jpg", new File(destPath));
        }
    }