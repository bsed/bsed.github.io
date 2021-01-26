---
title: "Tesseract: 从图片提取文本"
categories: [ "Linux" ]
tags: [ "linux","tesseract","ocr" ]
draft: false
slug: "tesseract-extract-text-from-the-picture"
date: "2015-07-23 15:17:00"
---

经常遇到同事要求排查故障，但却提供截图而不出示文本信息。为此，我考虑用 OCR（Optical Character Recognition，光学字符识别）技术从截图中将文本提取出来。通过试用和比较，我感觉 Tesseract 还不错，故在此略作推荐。Tesseract 原由 HP 实验室开发，后来开源，它不仅支持许多语言，而且识别效果也不错。

安装 `Tesseract`

在 Debian 上，可通过如下命令安装 Tesseract，其他 Linux 发行版可通过自身的包管理器安装：


<!--more-->


`apt-get install tesseract-ocr tesseract-ocr-chi-sim`

除了安装 Tesseract OCR 引擎之外，此处也安装了对简体中文语言的支持。

# 使用 Tesseract

Tesseract 需在命令行下使用，假如我想要从 wb.jpeg 这张图片中提取文本，那么可以执行：

 `tesseract wb.jpeg stdout -l chi_sim`

stdout 是将提取的文本打印到标准输出，如果文本较多则不妨将其放到文件中；-l 指定所用的语言 chi_sim（简体中文）。