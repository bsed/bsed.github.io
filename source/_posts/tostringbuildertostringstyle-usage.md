---
title: ToStringBuilder+ToStringStyle的用法
date: 2015-09-01 10:39:00
updated: 2015-12-07 17:53:07
tags: 
- mysql
- php
- ubuntu
- nginx
categories: 
- php

---
    package com.java.etl.codeTest;
    
    import org.apache.commons.lang.builder.ToStringBuilder;
    import org.apache.commons.lang.builder.ToStringStyle;
    
    
    public class Test {
     public static void main(String[] args) {
      Test t = new Test();
      String z = t.toString();
      System.out.println(z);
     }
     


<!--more-->


     
     public String toString() {
            return new ToStringBuilder(this, ToStringStyle.MULTI_LINE_STYLE)
                    .append("name", "a")
                    .append("age", "b")
                    .toString();
        }
    
    }

运行结果：

    com.java.etl.codeTest.Test@173a10f[
      name=a
      age=b
    ]