---
title: "Failed to open VDPAU backend libvdpau_i965.so: cannot open shared object file:"
categories: [ "Linux" ]
tags: [ "chrome","libvdpau_i965.so" ]
draft: false
slug: "failed-to-open-vdpau-backend-libvdpaui965so-cannot-open-shared-obxob-ject-file"
date: "2019-01-20 11:12:00"
---

解决 Failed to open VDPAU backend libvdpau_i965.so
调整 VLC 视频输出设置，详见
[https://wiki.archlinux.org/index.php/VL ... AU_backend](https://wiki.archlinux.org/index.php/VLC_media_player#Failed_to_open_VDPAU_backend)

```bash
 vdpauinfo 
display: :0.0   screen: 0
Failed to open VDPAU backend libvdpau_i965.so: cannot open shared object file: No such file or directory
Error creating VDPAU device: 1
```
<!--more-->




1. 安装 vdpauinfo，确认安装了 libvdpau-va-gl1 和 i965-va-driver nvidia 安装 vdpau-va-driver
2. 运行 vdpauinfo 看是否提示相同错误？(Failed to open VDPAU backend libvdpau_i965.so)
对于 amd64 架构系统
```bash
sudo ln -s /usr/lib/x86_64-linux-gnu/vdpau/libvdpau_va_gl.so.1 /usr/lib/x86_64-linux-gnu/vdpau/libvdpau_i965.so.1
```
3. 编辑/创建 /etc/profile 和 /etc/xprofile，都加入以下内容
```bash
    export VDPAU_DRIVER=va_gl
```    
4. 重新启动计算机
    运行 vdpauinfo 看错误是否消失？
```bash
└──╼ vdpauinfo
display: :0.0   screen: 0
libva info: VA-API version 1.2.0
libva info: va_getDriverName() returns 0
libva info: Trying to open /usr/lib/x86_64-linux-gnu/dri/i965_drv_video.so
libva info: Found init function __vaDriverInit_1_2
libva info: va_openDriver() returns 0
API version: 1
Information string: OpenGL/VAAPI backend for VDPAU

Video surface:

name   width height types
-------------------------------------------
420     4096  4096  NV12 YV12 UYVY YUYV Y8U8V8A8 V8U8Y8A8 
422     4096  4096  NV12 YV12 UYVY YUYV Y8U8V8A8 V8U8Y8A8 
444     4096  4096  NV12 YV12 UYVY YUYV Y8U8V8A8 V8U8Y8A8 

Decoder capabilities:

name                        level macbs width height
----------------------------------------------------
MPEG1                          --- not supported ---
MPEG2_SIMPLE                   --- not supported ---
MPEG2_MAIN                     --- not supported ---
H264_BASELINE                  51 16384  2048  2048
H264_MAIN                      51 16384  2048  2048
H264_HIGH                      51 16384  2048  2048
VC1_SIMPLE                     --- not supported ---
VC1_MAIN                       --- not supported ---
VC1_ADVANCED                   --- not supported ---
MPEG4_PART2_SP                 --- not supported ---
MPEG4_PART2_ASP                --- not supported ---
DIVX4_QMOBILE                  --- not supported ---
DIVX4_MOBILE                   --- not supported ---
DIVX4_HOME_THEATER             --- not supported ---
DIVX4_HD_1080P                 --- not supported ---
DIVX5_QMOBILE                  --- not supported ---
DIVX5_MOBILE                   --- not supported ---
DIVX5_HOME_THEATER             --- not supported ---
DIVX5_HD_1080P                 --- not supported ---
H264_CONSTRAINED_BASELINE      51 16384  2048  2048
H264_EXTENDED                  --- not supported ---
H264_PROGRESSIVE_HIGH          --- not supported ---
H264_CONSTRAINED_HIGH          --- not supported ---
H264_HIGH_444_PREDICTIVE       --- not supported ---
HEVC_MAIN                      --- not supported ---
HEVC_MAIN_10                   --- not supported ---
HEVC_MAIN_STILL                --- not supported ---
HEVC_MAIN_12                   --- not supported ---
HEVC_MAIN_444                  --- not supported ---

Output surface:

name              width height nat types
----------------------------------------------------
B8G8R8A8         16384 16384    y  
R8G8B8A8         16384 16384    y  
R10G10B10A2      16384 16384    y  
B10G10R10A2      16384 16384    y  
A8               16384 16384    y  

Bitmap surface:

name              width height
------------------------------
B8G8R8A8         16384 16384
R8G8B8A8         16384 16384
R10G10B10A2      16384 16384
B10G10R10A2      16384 16384
A8               16384 16384

Video mixer:

feature name                    sup
------------------------------------
DEINTERLACE_TEMPORAL             -
DEINTERLACE_TEMPORAL_SPATIAL     -
INVERSE_TELECINE                 -
NOISE_REDUCTION                  -
SHARPNESS                        -
LUMA_KEY                         -
HIGH QUALITY SCALING - L1        -
HIGH QUALITY SCALING - L2        -
HIGH QUALITY SCALING - L3        -
HIGH QUALITY SCALING - L4        -
HIGH QUALITY SCALING - L5        -
HIGH QUALITY SCALING - L6        -
HIGH QUALITY SCALING - L7        -
HIGH QUALITY SCALING - L8        -
HIGH QUALITY SCALING - L9        -

parameter name                  sup      min      max
-----------------------------------------------------
VIDEO_SURFACE_WIDTH              -  
VIDEO_SURFACE_HEIGHT             -  
CHROMA_TYPE                      -  
LAYERS                           -  

attribute name                  sup      min      max
-----------------------------------------------------
BACKGROUND_COLOR                 -  
CSC_MATRIX                       -  
NOISE_REDUCTION_LEVEL            -  
SHARPNESS_LEVEL                  -  
LUMA_KEY_MIN_LUMA                -  
LUMA_KEY_MAX_LUMA 
```
笔推荐扩展阅读：
[如何在Ubuntu启用Chromium硬件加速的视频解码](https://zhuanlan.zhihu.com/p/44423523)

[https://www.linuxuprising.com/2018/08/how-to-enable-hardware-accelerated.html](https://www.linuxuprising.com/2018/08/how-to-enable-hardware-accelerated.html)