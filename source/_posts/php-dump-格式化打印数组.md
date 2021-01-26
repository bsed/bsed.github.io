---
title: "php dump 格式化打印数组"
categories: [ "PHP" ]
tags: [ "php","dump" ]
draft: false
slug: "dump-php-format-print-arr"
date: "2011-09-05 13:48:00"
---

```php
/**
* 输出变量的内容，通常用于调试
*
* @package Core
*
* @param mixed $vars 要输出的变量
* @param string $label
* @param boolean $return
*/


<!--more-->


function dump($vars, $label = '', $return = false)
{
    if (ini_get('html_errors')) {
        $content = "<pre>\n";
        if ($label != '') {
            $content .= "<strong>{$label} :</strong>\n";
        }
        $content .= htmlspecialchars(print_r($vars, true));
        $content .= "\n</pre>\n";
    } else {
        $content = $label . " :\n" . print_r($vars, true);
    }
    if ($return) { return $content; }
    echo $content;
    return null;
}
```