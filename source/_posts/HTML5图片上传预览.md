---
title: "HTML5图片上传预览"
categories: [ "Css" ]
tags: [ "html5" ]
draft: false
slug: "html5-upload-preview"
date: "2013-05-20 20:36:00"
---

HTML5实现图片的上传预览，需要使用`FileReader`对象。

也就是说，使用FileReader对象先读取用户需要上传的图片，这个时候，图片是保存在浏览器中的，然后通过设置img元素的src，来预览图片，方法很简单。

在使用FileReader时需要先弄明白其Event handlers和方法。

## Event handlers ##

Event handler	描述
FileReader.onabort	A handler for the abort event. This event is triggered each time the reading operation is aborted.
FileReader.onerror	A handler for the error event. This event is triggered each time the reading operation encounter an error.
FileReader.onload	A handler for the load event. This event is triggered each time the reading operation is successfully completed.
FileReader.onloadstart	A handler for the loadstart event. This event is triggered each time the reading is starting.
FileReader.onloadend	A handler for the loadend event. This event is triggered each time the reading operation is completed (either in success or failure).
FileReader.onprogress	A handler for the progress event. This event is triggered while reading a Blob content.
Metchods

Method	描述
FileReader.abort()	Aborts the read operation. Upon return, the readyState will be DONE.
FileReader.readAsArrayBuffer()	Starts reading the contents of the specified Blob, once finished, the result attribute contains an ArrayBuffer representing the file's data.
FileReader.readAsBinaryString()	Starts reading the contents of the specified Blob, once finished, the result attribute contains the raw binary data from the file as a string.
FileReader.readAsDataURL()	Starts reading the contents of the specified Blob, once finished, the result attribute contains a data: URL representing the file's data.
FileReader.readAsText()	Starts reading the contents of the specified Blob, once finished, the result attribute contains the contents of the file as a text string.
所以只需要使用readAsDataURL()方法读取图片，绑定FileReader的onload事件，将读取的result中的url设置到img的src上

    <div><input id="upload" type="file"></div>
    <div><img id="pic" src=""></div>
    <script>
        var reader = new FileReader();
        reader.onload = function(e){
            document.getElementById('pic').setAttribute('src', e.target.result);
        };
        function readURL(input) {
            if (input.files && input.files[0]) {
                reader.readAsDataURL(input.files[0]);
            }
        };
        document.getElementById('upload').onchange = function(){
            readURL(this);
        };
    </script>