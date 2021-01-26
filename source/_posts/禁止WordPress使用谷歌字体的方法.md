---
title: "禁止WordPress使用谷歌字体的方法"
categories: [ "JS" ]
tags: [ "google","wordpress","fonts" ]
draft: false
slug: "prohibition-of-the-use-of-the-google-font-for-wordpress"
date: "2013-10-06 19:06:00"
---

最近谷歌在国内似乎遭到全面封杀，不仅搜索服务完全无法使用，也殃及到其他服务，其中一个受牵连的事情就是，最近在使用Wordpress时，页面加载异常缓慢，一个简单的页面需要等待很久，使用firebug查看发现，是浏览器一直在等待页面中嵌入的谷歌网络字体(google fonts)链接fonts.googleapis.com，直到超时。这说明fonts.googleapis.com这个网站也被封锁了。

我曾经在《网络字体(Web Font)的中文用法(一)》一文中说过，中文是很难使用网络字体的，更别说谷歌提供的英文网络字体了——基本上毫无用处。所以很奇怪为什么中文版的Wordpress里还引用了谷歌字体。

既然谷歌被封杀是无法避免的，那对于我们个人来说就只能让Wordpress禁止使用fonts.googleapis.com这个地址。让你如何去掉页面中嵌入的fonts.googleapis.com引用链接呢？

WordPress系统里有两个地方使用了谷歌字体，一个是主题里，一个是管理员界面。主题里的谷歌字体很好去掉，直接删除就行了，但管理员admin界面里的谷歌字体就不是这么容易了，因为我们不能修改wordpress系统文件(修改后的系统文件会在下一次升级时可能被覆盖掉。)


<!--more-->


在 `wp-includes/script-loader.php` 的564行(我使用的版本是3.9)，你能看到下面一段代码：

    if ( 'off' !== _x( 'on', 'Open Sans font: on or off' ) ) {
    		$subsets = 'latin,latin-ext';
    
    		/* translators: To add an additional Open Sans character subset specific to your language,
    		 * translate this to 'greek', 'cyrillic' or 'vietnamese'. Do not translate into your own language.
    		 */
    		$subset = _x( 'no-subset', 'Open Sans font: add new subset (greek, cyrillic, vietnamese)' );
    
    		if ( 'cyrillic' == $subset ) {
    			$subsets .= ',cyrillic,cyrillic-ext';
    		} elseif ( 'greek' == $subset ) {
    			$subsets .= ',greek,greek-ext';
    		} elseif ( 'vietnamese' == $subset ) {
    			$subsets .= ',vietnamese';
    		}
    
    		// Hotlink Open Sans, for now
    		$open_sans_font_url = "//fonts.googleapis.com/css?family=Open+Sans:300italic,400italic,600italic,300,400,600&subset=$subsets";
    	}

**很明显**，只要将`if ( 'off' !== _x( 'on', 'Open Sans font: on or off' ) ) {`这个判断语句不通过就行了，也就是说，只要将位于wp-content\languages目录下里的语言文件zh_CN.mo修改一下就行了。

首先打开zh_CN.po，找到里面的msgctxt "Open Sans font: on or off"这一行，将其下面的msgstr "on"改成msgstr "off"，保存，最后将这个修改后的zh_CN.po编译成zh_CN.mo——你可以使用在线编译工具[http://tools.konstruktors.com/](http://tools.konstruktors.com/)，或者使用Wordpress官方推荐的工具：PoEdit。

将编译后的zh_CN.mo替换原zh_CN.mo。

*修改前：*

    #. translators: If there are characters in your language that are not supported
    #. * by Open Sans, translate this to 'off'. Do not translate into your own
    #. language.
    #: wp-includes/script-loader.php:564
    msgctxt "Open Sans font: on or off"
    msgid "on"
    msgstr "on"

*修改后：*

    #. translators: If there are characters in your language that are not supported
    #. * by Open Sans, translate this to 'off'. Do not translate into your own
    #. language.
    #: wp-includes/script-loader.php:564
    msgctxt "Open Sans font: on or off"
    msgid "on"
    msgstr "off"
	
如果你觉得这个去掉谷歌字体的方法还是复杂，那么，你可以试一下[disable-google-fonts](https://github.com/dimadin/disable-google-fonts)这个Wordpress插件。