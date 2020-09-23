---
title: 解决lua在nginx 不能调试的问题
date: 2013-12-16 13:11:00
updated: 2015-01-13 13:12:38
tags: 
- css
- 优化
categories: 
- css

---
目前尝试在使用`ngx_lua`做简单的API接口，后端与mysql交互，但在调试sql语句时，每次修改都需要reload nginx，这样很繁琐。


<!--more-->


最近收到github的issues邮件通知，拿到这里分享下。

    你需要把 Lua 代码放在外部文件中，这样在 lua_code_cache off 时才不需要 reload nginx.
    
    如果你直接把 Lua 代码内联在 nginx.conf 配置文件中，则必须 reload nginx 才能生效，毕竟只有 nginx
    知道如何重新加载它自己的配置文件。这一点在 ngx_lua 官方文档的 lua_code_cache 一节中有详细的说明：
    
    http://wiki.nginx.org/HttpLuaModule#lua_code_cache

GitHub issues地址：https://github.com/agentzh/lua-resty-mysql/issues/8#issuecomment-19728005