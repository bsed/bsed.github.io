---
title:  JSTL截取字符串
date: 2009-10-25 14:06:00
updated: 2016-01-25 14:06:37
tags: 
- emacs
categories: 
- vim

---
在应用程序开发中,如果内容过长,想截取一定长度字符,然后补充"....."
jstl1.1引入了一个fn.tld的标签,用于处理字符,如获得字符length,substring,indexof,endWith,lowcase
实现截取字符串
如:
```jsp
    <c:set var="log.logTitle" value="做一个截取字符串长度的测试"
    <c:choose>
         <c:when test="${fn:length(log.logTitle) > 10}">
          <c:out value="${fn:substring(log.logTitle, 0, 10)}......" />
         </c:when>
         <c:otherwise>
          <c:out value="${log.logTitle}" />
         </c:otherwise>
        </c:choose>
```