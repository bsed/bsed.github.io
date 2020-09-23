---
title: 按需加载 Shortcode 相关脚本文件[转]
date: 2014-01-12 18:53:00
updated: 2015-07-30 18:55:59
tags: 
- css
- 秒杀
categories: 
- java

---
优化站点时一大核心思想就是“按需/动态加载”，PHP功能代码、jQuery特效或CSS样式，最理想的状态都是：当前页面需要时才加载进来，当前页面加载的都是必需的。

比如：jQuery 的 `$.getScript` 动态加载 js；WordPress的各种` is_`、`has_`内置函数；CSS一般是合并，只有存在个别页面非常特别时才会使用前两者来按需加载（《按需加载 CSS》），偶尔也用用 `Media Queries` 根据浏览器宽度动态加载（《动态改变主题宽度》）。

好，说的这些其实都是当初没用 [《Hermit 音乐播放器》](http://mufeng.me/hermit-for-wordpress.html) 以及之后用上到现在一直有些些不爽的原因。因为全站就一篇文章用了这个播放器，结果全站都需要加载一个css和一个js（共约60KB）。

有多少小伙伴有因为这个原因犹犹豫豫启用了又关了的请举手 ("▔□▔)/


<!--more-->


一开始自个儿还想用 jQuery 判断页面是否有 `.hermit` 来动态加载 js，这方面无法彻底解决 css 加载问题，否之。问了问 @mufeng 说可以“判断有木有 hermit 的短代码”，正解！

搜索到水煮鱼分享的 《只有指定的 Shortcode 存在时才载入相关脚本文件》 代码，如下

    function wpjam_shortcode_scripts(){
      	global $post;
      	if( has_shortcode( $post->post_content, 'your-shortcode') ){
        	wp_enqueue_script( 'whatever');
      	}
    }
    add_action( 'wp_enqueue_scripts', 'wpjam_shortcode_scripts');

用上后，只在文章内页有效，首页文章列表等完全无效。（看看代码也是。。。）

搜索的大半天，照着老外的这篇 《How To Load Scripts In WordPress Only If A Post Has A Shortcode》 一改，成了！（PHP小白，加个 foreach 也只能问谷婶的命。。。）

class.hermit.php 内改动片段如下：

	

    /**
    	 * 载入所需要的CSS和js文件
    	 */	
     
    	public function hermit_scripts() {
    		global $post,$posts;
    		foreach ($posts as $post) {
    			if ( has_shortcode( $post->post_content, 'hermit') ){
    				wp_enqueue_style('hermit-css', $this->base_dir . '/assets/style/hermit.min-1.4.2.css', array(), VERSION, 'screen');
    				// JS文件在最底部加载
    				wp_enqueue_script( 'hermit-js', $this->base_dir . '/assets/script/hermit.min-1.4.2.js', array(), VERSION, true);
    				wp_localize_script( 'hermit-js', 'hermit', 
    					array(
    						"url" => $this->base_dir . '/assets/swf/',
    						"nonce" => wp_create_nonce("hermit-nonce"),
    						"ajax_url" =>  admin_url() . "admin-ajax.php"
    				));
    			break;
    			};
    		}
    	}

改了后 Hermit 插件设置内的 “Javascript 和 CSS 加载” 随便选都无所谓啦，因为现在只会在使用了 hermit短代码的文章加载文件。这下放歌放得不要太哈皮咯！~

原文： [http://immmmm.com/load-scripts-only-has-a-shortcode.html](http://immmmm.com/load-scripts-only-has-a-shortcode.html)