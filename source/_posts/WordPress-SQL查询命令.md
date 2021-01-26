---
title: "WordPress SQL查询命令"
categories: [ "PHP" ]
tags: [ "wordpress" ]
draft: false
slug: "wordpress-sql-query-command"
date: "2018-12-11 13:06:00"
---

### 更改您的网址

```sql
UPDATE wp_options SET option_value = replace(option_value, 'http://www.myoldurl.com', 'http://www.mynewurl.com') WHERE option_name = 'home' OR option_name = 'siteurl';
```


<!--more-->


### 更新GUID（全局唯一标识符）

如果您已将您的博客网站从本地环境上传到新服务器或更换新的域名，则还应更新GUID字段的URL，因为这用于将文章或页面段落转换为绝对路径。

```sql
UPDATE wp_posts SET guid = REPLACE (guid, 'http://www.myoldurl.com', 'http://www.mynewurl.com');
```

### 更新内容中的网址

```sql
UPDATE wp_posts SET post_content = REPLACE (post_content, 'http://www.myoldurl.com', 'http://www.mynewurl.com');
```

### 更新图像路径

```sql
UPDATE wp_posts SET post_content = REPLACE (post_content, 'src="http://www.myoldurl.com', 'src="http://yourcdn.mynewurl.com');
```

### 为帖子添加一个新字段

```sql
INSERT INTO wp_postmeta (post_id, meta_key, meta_value)
SELECT ID AS post_id, 'UniversalCustomField'
AS meta_key 'MyNewCustomFieldValue AS meta_value
FROM wp_posts WHERE ID NOT IN
(SELECT post_id FROM wp_postmeta WHERE meta_key = 'UniversalCustomField')
'' AND post_type = 'post';
```

以下内容将对您网站上的所有页面都做同样的事情：

```sql
INSERT INTO wp_postmeta (post_id, meta_key, meta_value)
SELECT ID AS post_id, 'UniversalCustomField'
AS meta_key 'MyCustomFieldValue AS meta_value
FROM wp_posts WHERE ID NOT IN
(SELECT post_id FROM wp_postmeta WHERE meta_key = 'UniversalCustomField')
AND 'post_type' = 'page';
```

### 删除帖子元

```sql
DELETE FROM wp_postmeta WHERE meta_key = 'UselessMetaKey';
```

### 识别未使用的标签

如果您删除旧帖子，则每个旧帖子都会保留在数据库中。这个查询允许你识别所有的旧标签。

```sql
SELECT * From wp_terms wt
 INNER JOIN wp_term_taxonomy wtt ON wt.term_id=wtt.term_id WHERE wtt.taxonomy='post_tag' AND wtt.count=0;
```

### 删除垃圾邮件

如果你运行一个博客，你已经厌倦了所有垃圾评论堆积如山。这一个SQL命令将清除您标记为垃圾邮件的所有评论：

```sql
DELETE FROM wp_comments WHERE wp_comments.comment_approved = 'spam';
```

### 重置密码

```sql
UPDATE wp_users SET user_pass = MD5( 'new_password' ) WHERE user_login = 'myusername';
```

### 重新分配文章

```sql
UPDATE wp_posts SET post_author = 'new-author-id' WHERE post_author = 'previous-author-id';
```

### 删除修订

```sql
DELETE a,b,c FROM wp_posts a
LEFT JOIN wp_term_relationships b ON (a.ID = b.object_id)
LEFT JOIN wp_postmeta c ON (a.ID = c.post_id)
WHERE a.post_type = 'revision'
```

### 删除所有未批准的评论

```sql
DELETE FROM wp_comments WHERE comment_approved = 0
```

### 禁用对旧帖子的评论

```sql
UPDATE wp_posts SET comment_status = 'closed' WHERE post_date < '2018-12-11' AND post_status = 'publish';
```

### 将注释电子邮件导出为唯一记录

```sql
SELECT DISTINCT comment_author_email FROM wp_comments;
```

### 删除Pingback

```sql
DELETE FROM wp_comments WHERE comment_type = 'pingback';
```

### 删除特定URL中的评论

```sql
DELETE from wp_comments WHERE comment_author_url LIKE "%nastyspamurl%" AND wp_comments.comment_approved = 'spam';
```

### 将WordPress帖子更改为页面

将帖子更改为页面非常简单：

```sql
UPDATE wp_posts SET post_type = 'page' WHERE post_type = 'post'
```

如果你想再次改回它们：

```sql
UPDATE wp_posts SET post_type = 'post' WHERE post_type = 'page'
```

要更改单个帖子（页面），您必须在WHERE子句中包含正确的ID字段。

### 删除简码

```sql
UPDATE wp_post SET post_content = replace(post_content, '[trashshortcode]', '' ) ;
```

### 查找和替换发布内容

如果您发现需要更新或更改多个帖子中的现有文本，无论出于何种原因，最简单的方法是使用以下简单查询：

```sql
UPDATE wp_posts SET 'post_content'
= REPLACE ('post_content',
'OldText',
'NewText');
```

### 查找缺少字段的文章

如果您想查找所有帖子或页面，实际上您的新字段尚未添加，您可以再次使用此查询，并确保将“FIELD_NAME”更改为您要检查的字段的名称，或者将’post’改为’page’来查找未使用该字段的页面。

使用此MySQL查询来获取缺少自定义字段的所有帖子。为了本教程的目的，我们将查询post_type = page。您将不得不使用实际的自定义字段名称替换CUSTOM_FIELD_NAME。

```sql
SELECT wp_posts.ID, wp_postmeta.meta_key
FROM wp_posts
LEFT JOIN wp_postmeta ON wp_posts.ID = wp_postmeta.post_id
AND wp_postmeta.meta_key = 'CUSTOM_FIELD_NAME'
WHERE wp_postmeta.meta_key is null and wp_posts.post_type = 'post'
order by wp_posts.ID asc
```