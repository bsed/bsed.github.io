---
title: "Typecho 导航子类多层分类 [代码分享]"
categories: [ "PHP" ]
tags: [ "typecho","typecho教程" ]
draft: false
slug: "multi-level-classification-of-typecho-navigation-subclass-1"
date: "2019-10-25 09:03:00"
---

如何实现导航层次多级分类呢？ 例如在本主题适当位置插入下列代码，一般是在`header.php`文件里，
![typecho_submenu.png][1]


<!--more-->


CSS效果要自己写下：

```php
 <?php $this->widget('Widget_Metas_Category_List')->to($categorys); ?>
 <?php while($categorys->next()): ?>
 <?php if ($categorys->levels === 0): ?>
 <?php $children = $categorys->getAllChildren($categorys->mid); ?>
 <?php if (empty($children)) { ?>
 <li <?php if($this->is('category', $categorys->slug)): ?> class="active"<?php endif; ?>>
    <a href="<?php $categorys->permalink(); ?>" title="<?php $categorys->name(); ?>"><?php $categorys->name(); ?></a>
</li>
<?php } else { ?>
<li >
    <a  data-toggle="dropdown" href="#" data-target="#"><?php $categorys->name(); ?></a>
    <ul class="sub-menu">
        <?php foreach ($children as $mid) { ?>
        <?php $child = $categorys->getCategory($mid); ?>
        <li <?php if($this->is('category', $mid)): ?> class="active"<?php endif; ?>>
            <a href="<?php echo $child['permalink'] ?>" title="<?php echo $child['name']; ?>"><?php echo $child['name']; ?></a>
        </li>
        <?php } ?>
    </ul>
</li>
<?php } ?>
<?php endif; ?>
<?php endwhile; ?>
```![2019-10-25T01:01:08.png][2]


  [1]: https://imgs.gnux.cn/usr/uploads/2019/10/3788309484.png