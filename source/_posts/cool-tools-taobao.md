---
title: Taobao的 酷炫的工具条
date: 2015-08-11 17:54:00
updated: 2015-08-11 18:07:29
tags: 
- spring
- transaction
categories: 
- java

---
> Author：流云诸葛

效果图如下：
![taobao_toolbox.gif][1]

## 一、需要引入`jquery`库


## 二、引入css文件

### use-font.css


<!--more-->


    @font-face {
        font-family: 'iconfont';
      src: url('http://at.alicdn.com/t/font_1439271220_3361516.eot'); /* IE9*/
      src: url('http://at.alicdn.com/t/font_1439271220_3361516.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
      url('http://at.alicdn.com/t/font_1439271220_3361516.woff') format('woff'), /* chrome、firefox */
      url('http://at.alicdn.com/t/font_1439271220_3361516.ttf') format('truetype'), /* chrome、firefox、opera、Safari, Android, iOS 4.2+*/
      url('http://at.alicdn.com/t/font_1439271220_3361516.svg#iconfont') format('svg'); /* iOS 4.1- */
    }
    
    .iconfont {
        font-family: "iconfont" !important;
        font-size: 16px;
        font-style: normal;
        -webkit-font-smoothing: antialiased;
        -webkit-text-stroke-width: 0.2px;
        -moz-osx-font-smoothing: grayscale;
    }
    
    .icon-icon:before {
        content: "\e607";
    }
    
    .icon-youxi:before {
        content: "\e609";
    }
    
    .icon-hanglvxingye:before {
        content: "\e608";
    }
    
    .icon-baoxian:before {
        content: "\e603";
    }
    
    .icon-caipiao:before {
        content: "\e602";
    }
    
    .icon-dianying:before {
        content: "\e600";
    }
    
    .icon-unie65b:before {
        content: "\e60b";
    }
    
    .icon-licai:before {
        content: "\e606";
    }
    
    .icon-Partner_07:before {
        content: "\e60a";
    }
    
    .icon-yinle:before {
        content: "\e601";
    }
    
    .icon-shuidianmei:before {
        content: "\e605";
    }
    
    .icon-huochepiao:before {
        content: "\e604";
    }
    
    * {
        box-sizing: border-box;
    }
    
    body {
        font: 12px/1.5 tahoma, arial, 'Hiragino Sans GB', sans-serif;
    }
    /*设置整站的a元素，ul元素，li元素的默认样式*/
    a {
        text-decoration: none;
    }
    
    ul {
        list-style: none;
    }
    
    ul,
    li {
        margin: 0;
        padding: 0;
    }
    
    @-webkit-keyframes toTopFromBottom {
        49% {
            -webkit-transform: translateY(-100%);
        }
    
        50% {
            opacity: 0.3;
            -webkit-transform: translateY(40%);
        }
    
        51% {
            opacity: 1;
        }
      /* 假如动画动画声明的时候使用了forwards，在动画结束后，元素将保持在to定义的状态，如果没有定义to这个状态，即使配置了forwards，元素在动画结束后会回到起始状态
       意思就是本例中，不使用forwards也没有关系
      to {
        opacity: 0.3;
      } 
    */
    }
    
    .toolbox {
        width: 600px;
        height: 600px;
        position: fixed;
        top: 50%;
        left: 50%;
        margin-left: -300px;
        margin-top: -300px;
    }
    
    .toolbox p {
        margin: 0;
        padding: 0;
    }
    
    .toolbox ul {
        width: 291px;
        height: 232px;
    }
    
    .toolbox ul:before,
    .toolbox ul:after {
        content: " ";
        display: table;
    }
    
    .toolbox ul:after {
        clear: both;
    }
    
    .toolbox .tb-item {
        float: left;
        width: 59px;
        height: 78px;
        border: 1px solid #eaeaea;
        margin-left: -1px;
        margin-top: -1px;
        position: relative;
    }
    
    .toolbox .tb-item.tb-cs-2 {
        width: 117px;
    }
    
    .toolbox .tb-item.tb-rs-2 {
        height: 155px;
    }
    
    .toolbox .tb-item-wrapper {
        text-align: center;
    }
    
    .toolbox .tb-item-wrapper,
    .toolbox .tb-item-wrapper .tb-item-iwrapper {
        position: absolute;
        top: 0;
        right: 0;
        left: 0;
        bottom: 0;
    }
    
    .toolbox .tb-item-wrapper .tb-item-iwrapper {
        overflow: hidden;
    }
    
    .toolbox .tb-item-wrapper .tb-item-icon {
        display: block;
        color: #ff4400;
        font-size: 24px;
        margin-top: 12px;
    }
    
    .toolbox .tb-item-wrapper .tb-item-text {
        transition: color 0.3s ease-in-out;
        color: #6c6c6c;
    }
    
    .toolbox .tb-item-wrapper.tb-item-big .tb-item-icon {
        font-size: 60px;
        transition: font-size 0.3s ease-in-out;
    }
    
    .toolbox .tb-item-wrapper .tb-item-box {
        position: absolute;
        display: none;
        background-color: #fff;
        top: 76px;
        height: 155px;
        width: 291px;
        border: 1px solid #ff4400;
        z-index: 1;
    }
    
    .toolbox .tb-item-wrapper.tb-item-huafei .tb-item-box {
        left: -1px;
    }
    
    .toolbox .tb-item-wrapper.tb-item-youxi .tb-item-box {
        left: -117px;
    }
    
    .toolbox .tb-item-wrapper.tb-item-lvxing .tb-item-box {
        left: -175px;
    }
    
    .toolbox .tb-item-wrapper.tb-item-baoxian .tb-item-box {
        left: -233px;
    }
    
    .toolbox .tb-item-wrapper:hover .tb-item-text {
        color: #ff4400;
    }
    
    .toolbox .tb-item-wrapper.tb-item-hbox:hover {
        top: -1px;
        left: -1px;
        bottom: -1px;
        right: -1px;
        border: 1px solid #ff4400;
        z-index: 1;
    }
    
    .toolbox .tb-item-wrapper.tb-item-hbox:hover .tb-item-iwrapper {
        bottom: -1px;
        z-index: 2;
        background-color: #fff;
    }
    
    .toolbox .tb-item-wrapper.tb-item-big:hover .tb-item-iwrapper {
        bottom: 76px;
    }
    
    .toolbox .tb-item-wrapper.tb-item-hbox:hover .tb-item-box {
        display: block;
    }
    
    .toolbox .tb-item-wrapper.tb-item-big:hover .tb-item-icon {
        font-size: 24px;
    }
    
    .toolbox .tb-item-wrapper:hover .tb-item-icon {
        -webkit-animation: toTopFromBottom 0.3s forwards;
        -moz-animation: toTopFromBottom 0.3s forwards;
        animation: toTopFromBottom 0.3s forwards;
    }
    
    .toolbox.tb-on .tb-item-big .tb-item-icon {
        font-size: 24px;
    }


### html：

    <!DOCTYPE html>
    <html lang="en">
    <head>
    	<meta charset="UTF-8">
    	<title>toolbox</title>		
    	<script id="jquery_183" type="text/javascript" class="library" src="/js/sandbox/jquery/jquery-1.8.3.min.js">  </script>
    	<link rel="stylesheet" type="text/css" href="http://sandbox.runjs.cn/uploads/rs/59/omhmalua/use-font.css">
     <style type="text/css">
      body {
	background: #fff
      }
       div.toolbox {
	  height: 300px;
	  margin-top:-150px;
     }
     </style> 
    </head>
    <body>
    	<div class="toolbox">
    		<ul>
    			<li class="tb-item tb-rs-2 tb-cs-2">
    				<a class="tb-item-wrapper tb-item-big  tb-item-hbox tb-item-huafei" href="javascript:;" title="话费充值">
    					<div class="tb-item-iwrapper">
    						<i class="tb-item-icon iconfont icon-icon"></i>
    						<p class="tb-item-text">充值</p>
    					</div>
    					<div class="tb-item-box">
    					</div>
    				</a>
    			</li>
    			<li class="tb-item">
    				<a class="tb-item-wrapper tb-item-hbox tb-item-youxi" href="javascript:;" title="游戏充值">
    					<div class="tb-item-iwrapper">
    						<i class="tb-item-icon iconfont icon-youxi"></i>
    						<p class="tb-item-text">游戏</p>
    					</div>
    					<div class="tb-item-box">
    					</div>
    				</a>
    			</li>
    			<li class="tb-item">
    				<a class="tb-item-wrapper tb-item-hbox tb-item-lvxing" href="javascript:;" title="旅行">
    					<div class="tb-item-iwrapper">
    						<i class="tb-item-icon iconfont icon-hanglvxingye"></i>
    						<p class="tb-item-text">旅行</p>
    					</div>
    					<div class="tb-item-box">
    					</div>
    				</a>
    			</li>
    			<li class="tb-item">
    				<a class="tb-item-wrapper tb-item-hbox tb-item-baoxian" href="javascript:;" title="保险">
    					<div class="tb-item-iwrapper">
    						<i class="tb-item-icon iconfont icon-baoxian"></i>
    						<p class="tb-item-text">保险</p>
    					</div>
    					<div class="tb-item-box">
    					</div>
    				</a>
    			</li>
    			<li class="tb-item">
    				<a class="tb-item-wrapper" href="javascript:;" title="彩票">
    					<div class="tb-item-iwrapper">
    						<i class="tb-item-icon iconfont icon-caipiao"></i>
    						<p class="tb-item-text">彩票</p>
    					</div>
    				</a>
    			</li>
    			<li class="tb-item">
    				<a class="tb-item-wrapper" href="javascript:;" title="电影">
    					<div class="tb-item-iwrapper">
    						<i class="tb-item-icon iconfont icon-dianying"></i>
    						<p class="tb-item-text">电影</p>
    					</div>
    				</a>
    			</li>
    			<li class="tb-item">
    				<a class="tb-item-wrapper" href="javascript:;" title="外卖">
    					<div class="tb-item-iwrapper">
    						<i class="tb-item-icon iconfont icon-unie65b"></i>
    						<p class="tb-item-text">点外卖</p>
    					</div>
    				</a>
    			</li>
    			<li class="tb-item">
    				<a class="tb-item-wrapper" href="javascript:;" title="理财">
    					<div class="tb-item-iwrapper">
    						<i class="tb-item-icon iconfont icon-licai"></i>
    						<p class="tb-item-text">理财</p>
    					</div>
    				</a>
    			</li>
    			<li class="tb-item">
    				<a class="tb-item-wrapper" href="javascript:;" title="找服务">
    					<div class="tb-item-iwrapper">
    						<i class="tb-item-icon iconfont icon-Partner_07"></i>
    						<p class="tb-item-text">找服务</p>
    					</div>
    				</a>
    			</li>
    			<li class="tb-item">
    				<a class="tb-item-wrapper" href="javascript:;" title="音乐">
    					<div class="tb-item-iwrapper">
    						<i class="tb-item-icon iconfont icon-yinle"></i>
    						<p class="tb-item-text">音乐</p>
    					</div>
    				</a>
    			</li>
    			<li class="tb-item">
    				<a class="tb-item-wrapper" href="javascript:;" title="水电煤">
    					<div class="tb-item-iwrapper">
    						<i class="tb-item-icon iconfont icon-shuidianmei"></i>
    						<p class="tb-item-text">水电煤</p>
    					</div>
    				</a>
    			</li>
    			<li class="tb-item">
    				<a class="tb-item-wrapper" href="javascript:;" title="火车票">
    					<div class="tb-item-iwrapper">
    						<i class="tb-item-icon iconfont icon-huochepiao"></i>
    						<p class="tb-item-text">火车票</p>
    					</div>
    				</a>
    			</li>
    		</ul>
    	</div>
    	<script type="text/javascript">
    		(function() {
    			var $tb = $('.toolbox');
    			var toggleTb = function(e){
    				$('.toolbox').toggleClass('tb-on');	
    			}
    			$('.toolbox').on('mouseenter','.tb-item-hbox',toggleTb).on('mouseleave','.tb-item-hbox',toggleTb);
    		})();
    	</script>
    </body>
    </html>


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/3859321693.gif