---
title: Typecho 启用 Valine.js
date: 2018-08-02 16:59:00
updated: 2018-08-02 17:09:52
tags: 
- linux
- centos
- ntp
categories: 
- linux

---
Valine —— 基于Leancloud的快速、简洁且高效的无后端评论系统。

## 步骤

 1. 获取 `APP ID` 和 `APP KEY`
 2. 点击这里注册并登录`Leancloud`
 3. 创建应用 > 进入应用设置 > 选择应用 Key，获取你的APP ID 和APP KEY
 4. 为了数据安全，进入应用设置 > 安全中心 > 填写Web 安全域名

<!--more-->


如图：
![leancloud_01.png][1]
![leancloud_02.png][2]
## 页面中的设置
注释掉模板`comments.php`里面的html并在合适的位置添加如下内容：
```javascript
    <!--Leancloud 操作库:-->
    <script src="//cdn1.lncld.net/static/js/3.0.4/av-min.js"></script>
    <!--Valine 的核心代码库:-->
    <script src='//unpkg.com/valine/dist/Valine.min.js'></script>
<div class="comment"></div>
<script>
new Valine({
  el:'.comment', // Valine 的初始化挂载器
  app_id:'Your APP ID', // 这里填写上面得到的APP ID
  app_key:'Your APP KEY', // 这里填写上面得到的APP KEY
  placeholder:' ', // 留言框占位文字
  notify:false, // 评论邮件提醒
  verify:true, // 验证码
  region:'cn', // 存储节点(cn/us)
  path:window.location.pathname, // 当前文章页路径，区分不同文章页，以保证正确读取该文章页下的评论列表
  avatar:'retro', // Gravatar 头像展示方式
  pageSize:10 // 评论列表分页，每页条数
});
</script>
```
我的配置如下：
```javascript
<!--Leancloud 操作库:-->
<script src="//cdn1.lncld.net/static/js/3.0.4/av-min.js"></script>
<!--Valine 的核心代码库:-->
<script src='//unpkg.com/valine/dist/Valine.min.js'></script>
<div class="comment"></div>
<script>
  new Valine({
    el:'.comment', // Valine 的初始化挂载器
    app_id:'c1236RA7VWmfniniAUpgboXn-gzGzoHsz', // 这里填写上面得到的APP ID
    app_key:'LHzC2THvs8vejtKcW83Xes8V', // 这里填写上面得到的APP KEY
    placeholder:' ', // 留言框占位文字
    notify:false, // 评论邮件提醒
    verify:true, // 验证码
    region:'cn', // 存储节点(cn/us)
    path:window.location.pathname, // 当前文章页路径，区分不同文章页，以保证正确读取该文章页下的评论列表
    avatar:'retro', // Gravatar 头像展示方式
    pageSize:10 // 评论列表分页，每页条数
  });
</script>
```
评论数据管理
登录Leancloud应用管理 > 选择你创建的应用 > 存储 > 添加 Comment
如图:
![leancloud_03.png][3]


  [1]: https://imgs.gnux.cn/usr/uploads/2018/08/3575520311.png
  [2]: https://imgs.gnux.cn/usr/uploads/2018/08/284223878.png
  [3]: https://imgs.gnux.cn/usr/uploads/2018/08/4010594840.png