---
title: 使用github pages和Octopress搭建静态博客及配置
date: 2013-10-12 21:17:00
updated: 2015-10-19 21:18:43
tags: 
- js
- touchmove
categories: 
- js

---
> 初衷：其实，前不久才购买的域名和主机，使用wordpress搭建的博客，但是wordpress功能完善地有些笨拙，再加上购买的主机速度慢地难以忍受，连写博客的欲望都没有了。后来发现github pages和Octopress可以组合搭建静态博客，托管在github上，既稳定又方便，我只要关注博客就行了。所以这两天都在折腾。

## 1. 环境准备

### 1.1 下载软件：`RubyInstaller`，`Devkit`，`git`

先在下载地址[rubyinstaller](http://rubyinstaller.org/downloads/)看说明，当前建议下载1.9.3稳定版`RubyInstaller`：`Ruby 1.9.3-p484`和`DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe`。（我因为下载的时2.0版，后来配置的时候报错了，版本改为1.9.3就ok了）
git可以下载最新版的[msysgit](https://code.google.com/p/msysgit/downloads/list)即可。

### 1.2 安装ruby


<!--more-->


安装RubyInstaller，注意不要安装在带有空格的目录中，如C:\Program Files，否则后面有些命令可能会出错；安装时选择将可执行路径加入到PATH环境变量中。Devkit解压即可，然后进入devkit目录，执行安装命令：

	$ cd Devkit
	$ ruby dk.rb init
   	$ ruby dk.rb install	

### 1.3 安装python和pygments

python2.7: http://www.python.org/ftp/python/2.7.6/python-2.7.6.msi

pygments的安装：
先下载ez_setup.py脚本：https://pypi.python.org/pypi/setuptools#windows，然后执行：

	$ python ez_setup.py

会下载setuptools-2.2.tar.gz，解压并进入：

	$ cd dist\setuptools-2.2
	$ python easy_install.py pygments

### 1.4 安装Octopress

默认是官方的源，在国内比较慢，建议换成国内淘宝的ruby源：

	$ gem sources -a http://ruby.taobao.org
	$ gem sources -r http://rubygems.org
	$ gem sources -l

安装Octopress到指定的目录：

	git clone git://github.com/imathis/octopress.git /d/yousharp

打开安装目录/d/yousharp的Gemfile文件，将第一行的source改为淘宝的源：

	source "http://ruby.taobao.org"

### 1.5 安装bundler

	$ gem install bundler
	$ bundle install

### 1.6 安装主题，生成博客并预览

Octopress的[主题列表](http://opthemes.com/)，我使用的是：[Bootstrap theme](https://github.com/bkutil/bootstrap-theme)：

	$ git clone git://github.com/bkutil/bootstrap-theme.git bootstrap-theme
	$ cp -R bootstrap-theme $MY_OCTOBLOG/.themes/bootstrap
	$ rake install['bootstrap']
	$ rake generate
	$ rake preview
>访问[http://localhost:4000](http://localhost:4000)即可预览。

### 1.7 修改环境变量支持中文

新建环境变量：LANG 和 LC_ALL，值均为zh_CN.UTF_8


## 2. 托管到github pages

### 2.1 新建github repo

在[github](http://github.com)新建一个repo，命名为USERNAME.github.io，使用你github的用户名取代USERNAME，如：nkcoder.github.io。
	
### 2.2 github配置

	$ git config --global user.name "nkcoder"
	$ git config --global user.email "daniel5hbs@gmail.com"
	$ git config --global credential.helper cache
	$ git config --global credential.helper 'cache --timeout=3600'

> 注意：credential helper 只对https有效，对ssh无效。

生成rsa key，并添加到github的SSH keys中：

	$ ssh-keygen -t rsa -C "daniel5hbs"
	$ ssh -T git@github.com

### 2.3 博客发布

	$ rake setup_github_pages
	$ rake generate
	$ rake deploy
	访问nkcoder.github.io。


## 3. 新建博客或页面

	$ rake new_post['first-blog']
	$ rake new_page['about']
	$ rake generate
	$ rake preview 

> 说明：使用`$ rake preview`可以监视文件的变动，可以实时预览修改，但是如果修改了_config.yml或者该配置文件引用的文件发生了变化，则需要重新`$ rake generate`。


## 4. 主题优化

### 4.1 添加disqus评论
先去[disqus](http://disqus.com/)注册一个帐号，然后修改根目录/d/yousharp下的_config.yml文件，修改为：

	# Disqus Comments
	disqus_short_name: nkcoder
	disqus_show_comment_count: yes 
>注意：将nkcoder替换为你的disqus帐号。

### 4.2 代码高亮

首先下载安装python 2.x版本，然后修改_config.yml，修改为：

	pygments: true
>注意冒号后有个空格。

在需要高亮的代码块使用语法：

	{% highlight java %}
	your code
	{% endhighlight %}

或者使用三个反引号（左Alt键右边的键)：

	```java
	your code
	```
### 4.3. 在页面右侧添加分类等模块

系统自带的模块放在了目录：source/_include/asides下，我们自定义的模块放在目录：source/_include/custom/asides下。
模块一：分类及标签
使用插件[octopress-category-list](https://github.com/ctdk/octopress-category-list)
下载下来后，将category_list.rb放在plugins目录下，将category_list.html和category_cloud.html放在source/_include/custom/asides目录下；

模块二：最新博文
这是系统默认的模块，在source/_include/asides目录下的recent_posts.html，样式可以自己修改；

模块三：博客链接
在source/_include/custom/asides目录下新建blogroll.html，比如内容可以为：

	<section>
		<h4> Blogroll </h4>
		<ul>
			<li><a href="http://ifeve.com/" target="_blank">并发编程网</a></li>
			<li><a href="http://tengine.taobao.org/book/index.html#" target="_blank">Nginx开发从入门到精通</a></li>
			<li><a href="http://timyang.net/" target="_blank">Tim Yang</a></li>
		</ul>
	</section>

模块四：豆瓣阅读列表
新建douban.html，进入[豆瓣收藏秀](http://www.douban.com/service/badgemakerjs)生成js代码，将内容写入douban.html中，如：

	<section>
		<h4> Want To Read</h4>
		<div>
		<script type="text/javascript" src="http://www.douban.com/service/badge/daniel5hbs/?selection=random&amp;picsize=small&amp;hideself=on&amp;show=wishlist&amp;n=9&amp;cat=book&amp;columns=3"></script>
		</div>
	</section>

模块五：disqus最新评论
新建模块文件：recent_comments.html，写入disqus的js代码，如：

	<section>
		<h4> Recent Comments </h4>
		<div id="dsq-recentcomments" class="dsq-widget"><script type="text/javascript" src="http://disqus.com/forums/{{ site.disqus_short_name }}/recent_comments_widget.js?hide_avatars=0"></script></div>
	</section>

然后修改_config.yml文件，将需要在侧边显示的模块包含在default_asides里，如：

	default_asides: [custom/asides/category_list.html, asides/recent_posts.html, custom/asides/blogroll.html, custom/asides/douban.html, custom/asides/recent-comment.html]
>根据包含的顺序，自上向下地展示。

### 4.4 显示摘要

在文章中的某一行添加如下代码即可：

	<!--more-->

页面显示“Read on->"， 剩下的内容会隐藏。


## 5. 不同环境下博客的同步

如果电脑中的文件丢失了，或者换电脑了，或者需要在另一个环境下写博客，则需要将博客拷贝到本地，并配置环境。

### 5.1 分支的概念

Octopress默认有两个分支，一个是source，包含生成博客的所有文件，另一个是master，即博客本身。
当我们本地配置完后，master分支在目录_deploy中，因为以下划线开头，当我们向source分支提交时$ git push -u origin source时，master分支被忽略，而当我们使用rake部署时$ rake deploy，master分支则更新。

### 5.2 将博客拷贝到本地的步骤

如果当前没有python和ruby的环境，则需要执行本博客1.1到1.3中的步骤，配置环境；

首先将source分支拷贝到本地的博客目录：

	$ git clone -b source git@github.com:nkcoder/nkcoder.github.io.git yousharp

> 将其中的nkcoder替换为你自己的用户名，将yousharp替换为你的博客目录。

然后，将master分支拷贝到_deploy目录(github路径是相同的)：

	$ cd mybolg
	$ git clone git@github.com:nkcoder/nkcoder.github.io.git _deploy

如果是新环境（比如新的电脑），则需要配置环境，安装Ruby21-x64/DevKit-mingw64：(注意：将source修改为国内淘宝的)

	$ gem install bundler
	$ bundle install
	$ rake setup_github_pages

可能需要输入github repo的地址，

	Enter the read/write url for your repository
	(For example, 'git@github.com:nkcoder/nkcoder.github.io.git)

### 5.3 不同环境的同步

在切换环境之前，确保对所做的修改都提交了：

	$ ranke generate
	$ git add .
	$ git commit -am 'your comment'
	$ git push origin source
	$ rake deploy 

	切换环境之后，将所有的更新拷贝下来：

	$ cd myblog
	$ git pull origin source
	$ cd ./_deploy
	$ git pull origin master

		
**----------------------------------------------2014-12-16补充-----------------------------------------**

在新环境下配置blog时，不要忘了先安装ruby/devkit/bundler；然后，如果执行`rake generate`报类似如下的错误：

	$ rake generate
	## Generating Site with Jekyll
	unchanged sass/screen.scss
	Configuration from c:/Sites/my_project/_config.yml
	Building site: source -> public
	c:/Ruby193/lib/ruby/gems/1.9.1/gems/jekyll-0.11.0/lib/jekyll/convertible.rb:29:i
	n `read_yaml': invalid byte sequence in GBK (ArgumentError)
			from c:/Ruby193/lib/ruby/gems/1.9.1/gems/jekyll-0.11.0/lib/jekyll/post.r
	b:39:in `initialize'
			from c:/Sites/my_project/plugins/preview_unpublished.rb:23:in `new'
			from c:/Sites/my_project/plugins/preview_unpublished.rb:23:in `block
	 in read_posts'
			from c:/Sites/my_project/plugins/preview_unpublished.rb:21:in `each'
			from c:/Sites/my_project/plugins/preview_unpublished.rb:21:in `read_
	posts'
			from c:/Ruby193/lib/ruby/gems/1.9.1/gems/jekyll-0.11.0/lib/jekyll/site.r
	b:128:in `read_directories'
			from c:/Ruby193/lib/ruby/gems/1.9.1/gems/jekyll-0.11.0/lib/jekyll/site.r
	b:98:in `read'
			from c:/Ruby193/lib/ruby/gems/1.9.1/gems/jekyll-0.11.0/lib/jekyll/site.r
	b:38:in `process'
			from c:/Ruby193/lib/ruby/gems/1.9.1/gems/jekyll-0.11.0/bin/jekyll:250:in
	 `<top (required)>'
			from c:/Ruby193/bin/jekyll:19:in `load'
			from c:/Ruby193/bin/jekyll:19:in `<main>'
			
说明encoding有问题，在需要设置LC_ALL和LANG两个环境变量，Linux下为：

	$ set LC_ALL=en_US.UTF-8
	$ set LANG=en_US.UTF-8

windows下在*Computer->Properties->Advanced system settings新建两个环境变量LC_ALL和LANG，值都为en_US.UTF-8即可。

### 参考博文：（非常感谢）

+ [Clone Your Octopress to Blog From Two Places](http://blog.zerosharp.com/clone-your-octopress-to-blog-from-two-places/)
+ [Recent Comments in Octopress](http://arshad.github.io/blog/2012/05/04/recent-comments-in-octopress/)
+ [用Github和Octopress搭建博客](http://corey600.github.io/blog/2013/02/28/use-github-and-octopress-create-blog/)
+ [使用github + Octopress 搭建免费博客](http://www.yanjiuyanjiu.com/blog/20130401/)
