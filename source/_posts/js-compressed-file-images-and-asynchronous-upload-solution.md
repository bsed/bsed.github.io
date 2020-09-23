---
title: js压缩file图片并异步上传解决方案
date: 2015-10-17 10:17:00
updated: 2015-10-17 10:27:06
tags: 
- xmemcached
categories: 
- java

---

> 作者：铃不铃不铃 
> 原文链接：http://www.mjix.com/archives/1715.html

从app页面选择手机中的图片一般都比较大，动则N个M，然后页面就挂在那里玩了，接下来就是讨论解决方案，之道的img压缩方案就是使用canvas压缩图片，那现在问题变为canvas压缩图片后如何上传的问题了

## 从input file中读出图片的方法

    var loadImageFromFile = function(file, callback) {
    	var reader = new FileReader();
    	reader.onload = function (e) {
    		callback(e.target.result);
    	};
    	reader.readAsDataURL(file);
    };

*参考链接：* [https://hacks.mozilla.org/2011/01/how-to-develop-a-html5-image-uploader/](https://hacks.mozilla.org/2011/01/how-to-develop-a-html5-image-uploader/)

## 使用canvas压缩图片同上链接已给出


<!--more-->


## canvas转为blob格式

    var dataURItoBlob = function(dataURI){
    	var byteString;
    	if (dataURI.split(',')[0].indexOf('base64') >= 0)
    		byteString = atob(dataURI.split(',')[1]);
    	else
    		byteString = unescape(dataURI.split(',')[1]);
    	// separate out the mime component
    	var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0];
     
    	// write the bytes of the string to a typed array
    	var ia = new Uint8Array(byteString.length);
    	for (var i = 0; i < byteString.length; i++) {
    		ia[i] = byteString.charCodeAt(i);
    	}
    	return new Blob([ia], {type:mimeString});
    };

*参考链接：* [http://stackoverflow.com/questions/4998908/convert-data-uri-to-file-then-append-to-formdata](http://stackoverflow.com/questions/4998908/convert-data-uri-to-file-then-append-to-formdata)

## 异步上传，新建一个formdata对象，添加blob到formdata中

    var formdata = new FormData();
    formdata.append('imgfile', blob);
     
    var url = "upload.php";
    $.ajax({
    	type:'POST',
    	url:url,
    	data:formdata,
    	contentType:false,
    	processData:false,
    	success : function(jdata){
    		console.log(jdata);
    	}
    });

## 综上所述给一个Helper

    var ScaleImageHelper = {
    	//从FILE中加载img
    	loadImageFromFile : function(file, callback) {
    		var reader = new FileReader();
    		reader.onload = function (e) {
    			callback(e.target.result);
    		};
    		reader.readAsDataURL(file);
    	},
     
    	dataURItoBlob : function(dataURI){
    		var byteString;
    		if (dataURI.split(',')[0].indexOf('base64') >= 0)
    			byteString = atob(dataURI.split(',')[1]);
    		else
    			byteString = unescape(dataURI.split(',')[1]);
     
    		// separate out the mime component
    		var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0];
     
    		// write the bytes of the string to a typed array
    		var ia = new Uint8Array(byteString.length);
    		for (var i = 0; i < byteString.length; i++) {
    			ia[i] = byteString.charCodeAt(i);
    		}
     
    		return new Blob([ia], {type:mimeString});
    	},
     
    	resizeImgToCanvas : function(img, maxHeight, maxWidth){
    		maxHeight = maxWidth||640;
    		maxWidth = maxWidth||640;
    		var width = img.width;
    		var height = img.height;
     
    		if(width > height){
    			if (width > MAX_WIDTH) {
    				height *= MAX_WIDTH / width;
    				width = MAX_WIDTH;
    			}
    		}else{
    			if (height > maxHeight) {
    				width *= maxHeight / height;
    				height = maxHeight;
    			}
    		}
     
    		var canvas = document.createElement('canvas')
    		canvas.width = width;
    		canvas.height = height;
    		var ctx = canvas.getContext("2d");
    		ctx.drawImage(img, 0, 0, width, height);
    		return canvas;
    	},
     
    	canvasToBlob : function(canvas){
    		var dataURL = canvas.toDataURL('image/jpeg', 0.5);
    		return this.dataURItoBlob(dataURL);
    	},
     
    	post : function(url, formdata, callback){
    		$.ajax({
    			type:'POST',
    			url:url,
    			data:formdata,
    			contentType:false,
    			processData:false,
    			success : function(jdata){
    				console.log(jdata);
    			}
    		});
    	}
    };

测试案例如下:

    <input type="file" id="J-file" accept="image/*">

JS文件：

    //监听文件变化
    $('#J-file').on('change', function(){
    	if(this.files.length<1){
    		return ;
    	}
    	var file = this.files[0];
    	//从文件中加载图片
    	ScaleImageHelper.loadImageFromFile(file, function(src){
    		var img = document.createElement('img');
    		//插入图片到body中
    		//$(document.body).append(img);
    		//图片加载完成
    		img.onload = function(){
    			//通过canvas缩放图片
    			var canvas = ScaleImageHelper.resizeImgToCanvas(this, 10, 10);
    			//把canvas转成blob格式
    			var blob = ScaleImageHelper.canvasToBlob(canvas);
     
    			//插入到form中异步上传
    			var formdata = new FormData();
    			formdata.append('imgfile', blob);
    			ScaleImageHelper.post('./scale-canvas.php', formdata);
    		};
    		img.src = src;
    	});
    });