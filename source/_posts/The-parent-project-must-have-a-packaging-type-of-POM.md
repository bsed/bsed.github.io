---
title: "The parent project must have a packaging type of POM"
categories: [ "Maven" ]
tags: [ "java","maven" ]
draft: false
slug: "parent-project-must-have-a-packaging-type-of-pom-the"
date: "2013-10-10 03:21:00"
---

The parent project must have a packaging type of POM
在Eclipse中使用Maven添加模块时报错：The parent project must have a packaging type of POM

## 解决办法:
是将`pom.xml` 中的  `<packaging>jar</packaging>` 改成  `<packaging>pom</packaging>`