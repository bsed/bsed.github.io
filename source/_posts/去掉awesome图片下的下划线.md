---
title: "去掉awesome图片下的下划线"
categories: [ "Css" ]
tags: [ "css" ]
draft: false
slug: "underline-the-awesome-images"
date: "2013-02-04 11:09:00"
---

    i[class^="icon-"]:before {
    	display: inline-block;
    	text-decoration: none;
        padding-right: 3px;
    }