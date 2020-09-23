---
title: 修复TpCache一个无法触发缓存刷新的bug
date: 2015-11-17 20:34:00
updated: 2017-02-16 20:45:37
tags: 
- android
- react
- react-native
categories: 
- js

---
我现在在用的是TpCache的最新版本

长期使用一直遇到一个问题，就是登录状态下，去发表评论或者回复评论，没有触发缓存刷新

## bug的描述

插件后台开启，登录用户缓存失效
去任意一篇文章或者页面发表或者回复评论
然后退出登录
刚刚发表的评论没显示出来
## 解决bug

去Github下载最新版本的[TpCache](https://github.com/phpgao/TpCache)

解压然后用编辑器打开`Plugin.php`

在`public static function activate(){}`里，在return前，添加下面的代码
```php
Typecho_Plugin::factory('Widget_Feedback')->finishComment = array('TpCache_Plugin', 'login_comment_update');
```
添加后是这样的


<!--more-->


```
public static function activate()
{

    省略...

    //评论
    Typecho_Plugin::factory('Widget_Feedback')->finishComment = array('TpCache_Plugin', 'comment_update');
    Typecho_Plugin::factory('Widget_Feedback')->finishComment = array('TpCache_Plugin', 'login_comment_update');

    return '插件安装成功,请设置需要缓存的页面';
}
```
接着，找到comment_update所在的代码块位置，在其后面添加下面代码块
```
/**
 * 用户登录评论更新
 *
 * @access public
 * @param array $comment 评论结构
 * @param Typecho_Widget $post 被评论的文章
 * @param array $result 返回的结果上下文
 * @param string $api api地址
 * @return void
 */
public static function login_comment_update($comment)
{
    if (self::check_login()) return true;
    $req = new Typecho_Request();
    self::delete(str_replace($req->getRequestRoot(), '', $req->getReferer()));
}
```
保存，然后上传到服务器代替原来的文件

测试

这里的测试就是把上面的bug描述再走一次，测试结果是评论可以正常显示出来。

我现在用的解决方法，如下

找到
```php
self::delete(str_replace($req->getRequestRoot(), '', $req->getReferer()));
```
替换为
```php
self::$cache->flush();
```