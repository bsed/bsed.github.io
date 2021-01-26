---
title: "CSS3伪类：valid、invalid、required的使用"
categories: [ "Css" ]
tags: [ "css3" ]
draft: false
slug: "css3-pseudo-class-valid-invalid-required"
date: "2013-08-04 11:12:00"
---

过去验证表单会通过js和正则去判断填写的内容是否正确，如email的验证。
HTML5的出现为我们提供一些属性，不用编写js和正则即可解决这个检验表单内容。
`:required`
必须，那input不能为空的意思。

`:valid`
有效，即当填写的内容符合要求的时候触发。

`:invalid`
无效，即当填写的内容不符合要求的时候触发。


<!--more-->


valid、invalid、required的定义
**css:**

    /* basics */
    input:required {
        border: 1px solid blue;
    }
    input:valid {
        border: 1px solid green;
    }
    input:invalid {
        border: 1px solid red;
    }

**html:**

    <h2>:required</h2>
    <p><strong>Name:</strong> <input type="text" value="value" required=""></p>
    	
    <h2>:valid</h2>
    <p><strong>Email:</strong> <input type="email" value="foo@bar.com"></p>
    
    <h2>:invalid</h2>
    <p><strong>Email:</strong> <input type="email" value="you" required=""></p>