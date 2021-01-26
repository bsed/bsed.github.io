---
title: "with --enable-x11grab compile, Unknown input format: 'x11grab"
categories: [ "Linux" ]
tags: [ "ffmpeg" ]
draft: false
slug: "unknown-input-formatx11grab"
date: "2019-07-06 22:09:00"
---

with `--enable-x11grab` and the configure passed without any errors, but x11 input wasn't enabled (I still had the message "Unknown input format: 'x11grab'

解决:
将 `--enable-x11grab` 修改为： `--enable-libxcb`