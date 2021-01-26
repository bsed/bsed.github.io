---
title: "JS json转string "
categories: [ "JS" ]
tags: [ "json" ]
draft: false
slug: "json-js-string"
date: "2012-11-05 20:40:00"
---

新方法

    JSON.stringify(user)

兼容老浏览器


<!--more-->


    function Obj2str(o) {
        if (o == undefined) {
            return "";
        }
        var r = [];
        if (typeof o == "string") return "\"" + o.replace(/([\"\\])/g, "\\$1").replace(/(\n)/g, "\\n").replace(/(\r)/g, "\\r").replace(/(\t)/g, "\\t") + "\"";
        if (typeof o == "object") {
            if (!o.sort) {
                for (var i in o)
                    r.push("\"" + i + "\":" + Obj2str(o[i]));
                if (!!document.all && !/^\n?function\s*toString\(\)\s*\{\n?\s*\[native code\]\n?\s*\}\n?\s*$/.test(o.toString)) {
                    r.push("toString:" + o.toString.toString());
                }
                r = "{" + r.join() + "}"
            } else {
                for (var i = 0; i < o.length; i++)
                    r.push(Obj2str(o[i]))
                r = "[" + r.join() + "]";
            }
            return r;
        }
        return o.toString().replace(/\"\:/g, '":""');
    }

