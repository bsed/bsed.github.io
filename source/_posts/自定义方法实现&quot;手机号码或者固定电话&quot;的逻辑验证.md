---
title: "自定义方法实现&quot;手机号码或者固定电话&quot;的逻辑验证"
categories: [ "JS" ]
tags: [ "jquery","javascript","validate" ]
draft: false
slug: "custom-method-to-achieve-the-mobile-phone-number-or-fixed-phone-logic-verification"
date: "2014-10-21 18:29:00"
---

最近项目开发中遇到这样的需求“手机号码或者固话至少填写一个”，如下图所示:
![jquery.validate_mobile.png][1]
项目采用的jquery.validate.js验证组件，目前组件不支持这种“或”逻辑的验证，于是就自己定义一个


<!--more-->


```javascript
jQuery.validator.addMethod("phone", function(value, element) {
            var mobile = $("#mobile").val();// 手机号码
            var telephone = $("#telephone").val();// 固定电话
            var mobileRule = /^(13[0-9]|14[5|7]|15[0|1|2|3|5|6|7|8|9]|18[0-9]|170)\d{8}$/;
            var telephoneRule = /^\d{3,4}-?\d{7,9}$/;

            // 都没填
            if (isEmpty(mobile) && isEmpty(telephone)) {
                //自定义错误提示
                $("#receivingMobile_tip").addClass("errorHint").text("请填写固定电话或手机号码");
                return false;
            }
            var mobilePass = false;
            var telephonePass = false;
            // 手机填了、固定电话没填
            if (!isEmpty(mobile) && isEmpty(telephone)) {
                if (!mobileRule.test(mobile)) {
                    //自定义错误提示
                    $("#receivingMobilePhone_tip").removeClass("successHint").addClass("errorHint").text("手机号码格式不对");
                    return false;
                } else {
                    mobilePass = true;
                }
            }

            // 手机没填、固定电话填了
            if (isEmpty(mobile) && !isEmpty(telephone)) {
                if (!telephoneRule.test(telephone)) {
                    //自定义错误提示
                    $("#receivingTelephone_tip").removeClass("successHint").addClass("errorHint").text("固定电话格式不对");
                    return false;
                } else {
                    telephonePass = true;
                }
            }

            if (mobilePass || telephonePass) {
                //自定义成功提示
                $("#receivingTelephone_tip").removeClass("errorHint").addClass("successHint").text('');
                return true;
            } else {
                return false;
            }
        }, "ignore");
```
补充isEmpty函数：
```javascript
 // 空字符串判断
function isEmpty(v, allowBlank) {
      return v === null || v === undefined || (!allowBlank ? v === "" : false);
}
```

处理validate的errorPlacement：
```javascript
errorPlacement : function(error, element) {
                //忽略自定义的方法错误提示
                if (error.text() == "ignore") {
                    return;
                }
　　　　　　　　　
            }
```

在rules里面使用 
```javascript
rules : {
                telephone : {
                    phone : []
                },
                mobile : {
                    phone : []
                }
            }
```
  [1]: https://imgs.gnux.cn/usr/uploads/2016/06/4293763603.png