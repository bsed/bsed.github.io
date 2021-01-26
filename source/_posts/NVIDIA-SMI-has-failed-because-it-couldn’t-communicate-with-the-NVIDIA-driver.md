---
title: "NVIDIA-SMI has failed because it couldn’t communicate with the NVIDIA driver"
categories: [ "Linux" ]
tags: [ "nvidia","modprobe","nvidia-smi" ]
draft: false
slug: "nvidiasmi-has-failed-because-it-couldnt-communicate-with-the-nvidia-driver"
date: "2018-04-18 06:31:00"
---

## 方案一：禁用安全启动选项

[https://stackoverflow.com/questions/42348400/nvidia-smi-failed-couldnt-communicate-with-nvidia-driver](https://stackoverflow.com/questions/42348400/nvidia-smi-failed-couldnt-communicate-with-nvidia-driver)


<!--more-->


## 方法二：`sudo modprobe nvidia` ，然后再运行`nvidia-smi`.

## 方法三：

```bash
sudo apt-get update
sudo apt-get install --no-install-recommends nvidia-384 libcuda1-384 nvidia-opencl-icd-384
sudo reboot
```