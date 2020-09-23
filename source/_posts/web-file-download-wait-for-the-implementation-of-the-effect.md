---
title:  Web文件下载等待效果的实现[转]
date: 2015-09-27 20:53:00
updated: 2015-09-27 20:54:04
tags: 
- linux
- mingw
categories: 
- linux

---
> 原文：http://blog.csdn.net/accountwcx/article/details/46728563  
> 作者：accountwcx  

项目中需要实现导出Excel的功能，Excel不是保存在服务器上的文件，而是动态生成的。由于查询条件和数据大小的影响，生成Excel的时间不固定（大约5~10秒左右），因此需要做下载等待效果，防止用户多次点击下载。实现下载等待效果的关键是获取到后台导出Excel完成的时间或者捕获到Excel开始下载的事件。可以从两个方面入手，一个是在浏览器端触发onload事件。一个是在服务器端完成Excel导出，然后把Excel保存到服务器的临时位置，然后通知客户端去下载。

## iframe实现下载等待

用iframe实现下载等待的原理是把下载的路径给iframe的src，然后监听iframe的onload事件，当后台处理完成并返回文件时，会触发iframe的onload事件。使用该方法有两个问题：

 - 参数通过url传递，如果url长度超过2048会被浏览器截断。
 - iframe的onload事件在ie浏览器下触发不符合要求。


<!--more-->


```
    <!DOCTYPE html>
    <html lang="zh">
    
    <head>
    	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    	<script type="text/javascript" src="http://cdn.bootcss.com/jquery/1.11.3/jquery.min.js"></script>
    <span style="white-space:pre">	</span><script type="text/javascript" src="http://cdn.bootcss.com/jquery.blockUI/2.66.0-2013.10.09/jquery.blockUI.min.js"></script>
    	<title>文件下载等待</title>
    </head>
    
    <body>
    	<button id="btnDownload">文件下载</button>	
    	<!--
    		下载的iframe
    		通过iframe的onload事件判断后台是否已经导出完成，浏览器是否开始下载
    	-->
    	<iframe id="iframeDownload" style="display:none;"></iframe>
    	
    	<script type="text/javascript">		
    		$(function(){
    			$('#iframeDownload').on('load', function(){
    				//当后台返回文件时，取消Loading。
    				$.unblockUI();
    				return false;
    			});
    			
    			$('#btnDownload').click(function(){
    				var url = 'https://codeload.github.com/mugifly/jquery-simple-datetimepicker/legacy.zip/1.12.0';
    				download(url, '下载中，请稍候。。。');
    				return false;
    			});
    		});
    		
    		/**
    		 * 下载
    		 * @param  url      {String}  [必填]下载文件的路径，如果有参数，请通过url传值，如download.jsp?year=2015&month=7
    		 * @param  message  {String}  [可选]等待文字，默认是请稍候
    		 */
    		function download(url, message){			
    			//如果message没有值或者不是字符串，使用默认等待文字。
    			if(!message || Object.prototype.toString.call(message) !== '[object String]'){
    				message = '请稍候。。。';
    			}
    			
    			//显示等待效果
    			$.blockUI({
    				message: message
    			});
    			
    			//让iframe的src调用url
    			$('#iframeDownload').attr('src', url);
    		}
    	</script>
    </body>
    
    </html>
```

上面的方法通过url传递参数，参数如果超过长度，则会被截断。可以通过form和iframe组合的方式解决，因为form通过post提交，不受参数大小的限制。具体思路是把需要提交的参数通过input放到form里面去，通过form的action动态指定下载文件的url，form的target指向iframe使得服务器的结果返回到iframe中，这样就能够触发iframe的onload事件。这个方法也存在ie下的兼容性问题。

```
<!DOCTYPE html>
<html lang="zh">

<head>
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
	<script type="text/javascript" src="http://cdn.bootcss.com/jquery/1.11.3/jquery.min.js"></script>
	<script type="text/javascript" src="http://cdn.bootcss.com/jquery.blockUI/2.66.0-2013.10.09/jquery.blockUI.min.js"></script>
	<title>文件下载等待</title>
</head>

<body>
	<button id="btnDownload">文件下载</button>	
	<!--
		通过form提交参数，把服务器返回的结果在iframe中显示
	-->
	<form id="formDownload" method="post" target="downloadTarget" style="display:none;">
		<iframe id="iframeDownload" name="downloadTarget"></iframe>
		<div class="form-params">
			<!-- 提交到后台的参数，如果有多个参数，可以用多个hidden -->
			<input type="hidden" name="param" value="" />
		</div>
	</form>
	
	<script type="text/javascript">		
		$(function(){
			$('#iframeDownload').on('load', function(){
				//当后台返回文件时，取消Loading。
				$.unblockUI();
				return false;
			});
			
			$('#btnDownload').click(function(){
				var url = 'https://codeload.github.com/mugifly/jquery-simple-datetimepicker/legacy.zip/1.12.0';
				download(url, [
					{
						name: 'time',
						value: new Date().getTime()
					},
					{
						name: 'year',
						value: new Date().getFullYear()
					}
				], '下载中，请稍候。。。');
				return false;
			});
		});
		
		/**
		 * 下载
		 * @param  url      {String}  [必填]下载文件的路径		 
		 * @param  params   {Object}  [可选]提交到后台的参数，如[{name:'year',value:2015},{name:'month',value:7}]
		 * @param  message  {String}  [可选]等待文字，默认是请稍候
		 */
		function download(url, params, message){
			//如果message没有值或者不是字符串，使用默认等待文字。
			if(!message || Object.prototype.toString.call(message) !== '[object String]'){
				message = '请稍候。。。';
			}
			
			//显示等待效果
			$.blockUI({
				message: message
			});
			
			var $form = $('#formDownload');
			
			//更换form的请求路径
			$form.attr('action', url);
			
			//把form下的全部参数去掉
			$form.children('.form-params').remove();
			
			var k, $params, $input;
			if(Object.prototype.toString.call(params) === '[object Array]'){
				//把新的参数添加到form下以便提交
				$params = $('<div class="form-params"></div>');
				$form.append($params);
				
				$.each(params, function(i, v){
					//创建隐藏的input
					$input = $('<input type="hidden" name="' + v.name + '" />');
					$input.val(v.value);
					$params.append($input);
				});
			}
			
			//提交请求
			$form.submit();
		}
	</script>
</body>

</html>
```