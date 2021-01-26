---
title: "OpenSans 字体及下载"
categories: [ "Css" ]
tags: [ "css","fonts","opensans" ]
draft: false
slug: "opensans-font"
date: "2015-10-04 09:58:00"
---

自己复制出来 写个CSS 修改一下 里面的URL 就行了 要把URL对应的4个文件下载回来... 

    @font-face {
      font-family: 'Open Sans';
      font-style: normal;
      font-weight: 300;
      src: local('Open Sans Light'), local('OpenSans-Light'), url(http://themes.googleusercontent.com/static/fonts/opensans/v9/DXI1ORHCpsQm3Vp6mXoaTb3hpw3pgy2gAi-Ip7WPMi0.woff) format('woff');
    }


<!--more-->


    @font-face {
      font-family: 'Open Sans';
      font-style: normal;
      font-weight: 400;
      src: local('Open Sans'), local('OpenSans'), url(http://themes.googleusercontent.com/static/fonts/opensans/v9/wMws1cEtxWZc6AZZIpiqWALUuEpTyoUstqEm5AMlJo4.woff) format('woff');
    }
    @font-face {
      font-family: 'Open Sans';
      font-style: normal;
      font-weight: 600;
      src: local('Open Sans Semibold'), local('OpenSans-Semibold'), url(http://themes.googleusercontent.com/static/fonts/opensans/v9/MTP_ySUJH_bn48VBG8sNSr3hpw3pgy2gAi-Ip7WPMi0.woff) format('woff');
    }
    @font-face {
      font-family: 'Open Sans';
      font-style: normal;
      font-weight: 700;
      src: local('Open Sans Bold'), local('OpenSans-Bold'), url(http://themes.googleusercontent.com/static/fonts/opensans/v9/k3k702ZOKiLJc3WVjuplzL3hpw3pgy2gAi-Ip7WPMi0.woff) format('woff');
    }
