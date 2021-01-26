---
title: "防止重复提交的TOKTN()方法的运用"
categories: [ "Java" ]
tags: [ "jsp","token" ]
draft: false
slug: "the-application-of-toktn-method-to-prevent-duplication"
date: "2011-09-01 13:06:00"
---

 JSP页面防止重复提交的TOKTN()方法的运用:
 1. 在你第一个页面中的Action中加入:saveToken(request);
 2. 在你接着要进入的页面的Acion中加入代码:

    if (isTokenValid(request)) {
       resetToken(request);
       LOG.debug("Token 验证成功。");
      } else {
       saveErrors(request, errors);
       saveToken(request);
       LOG.error("操作重复!");
    
      // return mapping.findForward("reply");//这里根据你自己的目标页面
      }


----------


3.在你操作的第二个页面中的<HTML:FORM> </HTML:FORM>之间加入: 

    <input type="hidden" name="org.apache.struts.taglib.html.TOKEN" value="<bean:write name="org.apache.struts.action.TOKEN" />" />