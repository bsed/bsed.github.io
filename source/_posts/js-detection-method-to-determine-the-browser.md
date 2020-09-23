---
title: JS检测判断浏览器的方法
date: 2015-07-06 11:53:00
updated: 2017-01-02 17:40:03
tags: 
- golang
- formfile
categories: 
- go

---
```
function getBrowser(elements) {
        var userAgent = navigator.userAgent;
    var regexps = {
                'Chrome': [ /Chrome\/(\S+)/ ],
                'Firefox': [ /Firefox\/(\S+)/ ],
                'MSIE': [ /MSIE (\S+);/ ],
                'Opera': [ /Opera\/.*?Version\/(\S+)/,          /* Opera 10 */
                                   /Opera\/(\S+)/  ],                   /* Opera 9 and older */
                'Safari': [ /Version\/(\S+).*?Safari\// ]
        },
        re, m, browser, version;
 


<!--more-->


    if (elements === undefined)
        {
        elements = 2;
        }
    else if (elements === 0)
        {
        elements = 1337;
        }
 
    for (browser in regexps)
        {
        while (re = regexps[browser].shift())
                {
            if (m = userAgent.match(re)) 
                        {
                version = (m[1].match(new RegExp('[^.]+(?:\.[^.]+){0,' + --elements + '}')))[0];
                                browserObj = { name : browser, version : version }
                                return browserObj;
            }
                }
        }
        return null;
}
```