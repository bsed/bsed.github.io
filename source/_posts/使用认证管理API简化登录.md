---
title: "使用认证管理API简化登录"
categories: [ "JS" ]
tags: [ "javascript","api","auth" ]
draft: false
slug: "using-authentication-management-api-to-simplify-login"
date: "2016-06-12 20:49:00"
---

> 本文转载自：[众成翻译](http://www.zcfy.cc)
> 译者：[Melon.H](http://www.zcfy.cc/@melonHuang)
> 链接：[http://www.zcfy.cc/article/224](http://www.zcfy.cc/article/224)
> 原文：[https://developers.google.com/web/updates/2016/04/credential-management-api?hl=en](https://developers.google.com/web/updates/2016/04/credential-management-api?hl=en)

为了提供优质的用户体验，我们应该帮助用户在不同网页进行身份认证。完成认证的用户可以在不同网页拥有不同的资料，在多设备之间同步数据，或是在离线情况下处理数据；可以扩展的功能数之不尽。但是创建账号、记住和输入密码对用户来说非常麻烦，尤其是在手机端，这通常导致用户在不同网站使用同一份密码。这在安全性上有很大的风险。

Chrome 51(最新版本)支持了**[Credential Management API（证书管理API）](http://w3c.github.io/webappsec-credential-management/)**。它遵循了W3C提议的标准，为开发者提供了管理浏览器认证的入口，以帮助用户以更简单的方式登录。


<!--more-->


# 认证管理API的设计目标

通过认证管理API, 开发者可以保存和获取密码认证信息和联合认证信息。它提供了一下3个方法：

*   `navigator.credentials.get()`
*   `navigator.credentials.store()`
*   `navigator.credentials.requireUserMediation()`

通过使用这些API，开发者可以完成以下强大的功能：

*   让用户可以通过一次点击完成登录
*   记录用户登录时使用的联合认证账号
*   当session过期时，帮助用户重新登录


在Chrome的实现中，认证信息会被保存在Chrome的密码管理器中。用户登陆后，可以在多设备之间同步密码。同步的密码也可以被分享给Android应用，只要这些应用集成了[Smart Lock for Passwords for Android](https://developers.google.com/identity/smartlock-passwords/android/)，[以提供无缝的跨平台体验](https://developers.google.com/identity/smartlock-passwords/android/).

# 在网站中集成认证管理API

如何使用认证管理API，取决于你的网站架构是如何设计的。你的网站是一个单页应用？是一个涉及页面切换的传统架构？只能在首页登录？还是到处都能登录？用户可否在不登录的情况下浏览网页？登录操作是否在弹出的窗口中进行？还是说需要操作不同的页面才能登录。

我们不可能覆盖所有的场景，但让我们先通过一个典型的单页应用了解一下：

*   首页是一个登录表单。
*   点击“登录”按钮时，用户会被导向一个登录表单。
*   注册和登录表单都提供了id/password认证与联合认证登录的选项，例如：通过Google登录，或通过Facebook登录。


使用认证管理API，我们可以为网站添加以下功能，例如：

*   **在用户登录时，显示账号选择框： ** 当用户点击“登录”时，弹出原生账号选择框。
*   **保存认证信息：** 在用户成功登录后，使用浏览器的密码管理器保存认证信息，以便日后使用。
*   **调解自动登录：** 一旦用户退出登录，在用户下一次访问时，停止自动登录。

你们可以通过[这个例子](https://credential-management-sample.appspot.com/)体验一下，它的代码在[这儿](https://github.com/GoogleChrome/credential-management-sample).

**注意：这些API只能在安全网站中使用，例如HTTPS网站、locolhost**

## 登录时显示账号选择框

在用户点击了“登录”按钮，并跳到登录表单之前，你可以通过[navigator.credentials.get()](https://developer.mozilla.org/en-US/docs/Web/API/CredentialsContainer/get)方法获得认证信息。Chrome会弹出一个账号选择框，供用户选择。

![api_simple_login_01.png][1]  
_弹出了一个账号选择框，用户可选择一个账号进行登录。_

### 获取密码认证对象

如果想支持通过密码认证登录，请使用`password: true`

```javascript
navigator.credentials.get({  
  password: true, // 设置为true，以获取密码认证对象
}).then(function(cred) {  
  // 继续
  ...
```

### 使用密码认证登录

当用户选择了一个账号时，resolve函数会收到一个密码认证对象，你可以通过`fetch()`方法将信息发送到服务器。

```javascript
// 延续上一个例子
}).then(function(cred) {  
  if (cred) {  
    if (cred.type == 'password') {  
	  // 构建FormData对象
      var form = new FormData();

	  // 添加CSRF令牌
      var csrf_token = document.querySelector('csrf_token').value;  
      form.append('csrf_token', csrf_token);

	  // 你可以将额外信息添加到`.additionalData`
      cred.additionalData = form;

	  // 将认证对象作为`credentials`通过`POST`请求发送
	  // id, 密码和额外信息会被加密，并作为HTTP主体被发送给接口
      fetch(url, {           // 请保证url是HTTPS链接
        method: 'POST',      // 使用POST方法
        credentials: cred    // 添加密码认证对象
      }).then(function() {  
        // 继续  
      });  
    } else if (cred.type == 'federated') {  
      // 继续
```

### 使用联合认证登录

如果想为用户展示联合登录账号，请在调用`get()`方法时，通过`federated`选项定义一个包含账号提供者id的数组。

![api_simple_login_02.png][2]
_密码管理器保存了多个账号_


```javascript
navigator.credentials.get({  
  password: true, // 设置为true，以获得密码认证对象
  federated: {  
    providers: [  // 定义一个由联合账号供应者id组成的数组
      'https://accounts.google.com',  
      'https://www.facebook.com'  
    ]  
  }  
}).then(function(cred) {  
  // continuation  
  ...
```

通过认证对象的`type`属性，可以检查它的类型是`PasswordCredential`(`type == 'password'`)还是`FederatedCredential`(`type == 'federated'`)。
如果是[`FederatedCredential`](https://developer.mozilla.org/en-US/docs/Web/API/FederatedCredential)，你可以将它包含的信息发送给对应的API。

```javascript
});  
    } else if (cred.type == 'federated') {  
	  // `provider`属性包含了联合账号供应者的id
      switch (cred.provider) {  
        case 'https://accounts.google.com':  
		  // 使用Google Sign-In进行联合登录
          var auth2 = gapi.auth2.getAuthInstance();

		  // 使用Google Sign-In库时，可以通过login_hint指定一个账号
          return auth2.signIn({  
            login_hint: cred.id || ''  
          }).then(function(profile) {  
            // 继续  
          });  
          break;

        case 'https://www.facebook.com':  
		  // 使用Facebook Login进行联合登录
		  // 继续
          break;

        default:  
		  // 显示表单
          break;  
      }  
    }  
  // 如果cred是undefined
  } else {  
  	// 显示表单
```


![api_simple_login_03.png][3]

## 保存认证对象

当用户通过表单登录你的网站时，你可以使用[navigator.credentials.store()](https://developer.mozilla.org/en-US/docs/Web/API/CredentialsContainer/store)来保存认证信息。浏览器会询问用户是否希望保存。你可以根据认证的类型，决定使用[`new PasswordCredential()`](https://developer.mozilla.org/en-US/docs/Web/API/PasswordCredential)还是[`new FederatedCredential()`](https://developer.mozilla.org/en-US/docs/Web/API/FederatedCredential)来创建认证对象并保存。

![api_simple_login_04.png][4]
_Chrome询问用户是否希望保存认证信息（或是作为联合账号）_


### 通过表单元素创建和保存密码认证对象

以下代码通过属性`autocomplete`，自动将表单元素 [映射](http://w3c.github.io/webappsec-credential-management/#passwordcredential-form-constructor)为[PasswordCredential](https://developer.mozilla.org/en-US/docs/Web/API/PasswordCredential)对象的参数。


HTML
```html
&lt;form id="form" method="post"&gt;
  &lt;input type="text" name="id" autocomplete="username" /&gt;  
  &lt;input type="password" name="password" autocomplete="current-password" /&gt;  
  &lt;input type="hidden" name="csrf_token" value="******" /&gt;
&lt;/form&gt;
```


JavaScript: 
```javascript
var form = document.querySelector('\#form');  
var cred = new PasswordCredential(form);  
// 保存认证对象
navigator.credentials.store(cred)  
.then(function() {  
  // 继续
});
```

### 创建和保存联合认证对象

```javascript
// 在联合认证后，通过你获得的信息创建一个FederatedCredential对象
var cred = new FederatedCredential({  
  id: id,                                  // 用户id
  name: name,                              // 可选的用户名
  provider: 'https://accounts.google.com',  // 联合账号提供者的id
  iconURL: iconUrl                         // 可选的用户头像地址
});  
// 保存认证对象
navigator.credentials.store(cred)  
.then(function() {  
  // 继续
});
```

![api_simple_login_05.png][5]
## 使用户自动重新登录

当用户再次访问网站时，session有可能已经失效了。我们不必麻烦用户每次回访的时候重新输入密码。可以帮助他们自动重新登录。

![api_simple_login_06.png][6]
_当用户自动登录时，会弹出一个通知_

### 获取一个认证对象

```javascript
navigator.credentials.get({  
  password: true,
  federated: {
    providers: [
      'https://accounts.google.com',  
      'https://www.facebook.com'  
    ]  
  },  
  unmediated: true // 设置为true，以支持用户自动登录  
}).then(function(cred) {  
  if (cred) {  
	// 可以自动登录
    ...  
  } else {  
	// 不可以自动登录
    ...
  }  
});
```


这段代码与你在前面的“显示账号选择框”部分的代码相似。唯一的区别是这儿设置了`unmediated: true`。

这会使函数马上`resolve`，并返回一个认证对象，帮助用户自动登录。自动登录有以下条件：

*   浏览器已显式地告知用户正在进行自动登录
*   用户曾经通过认证管理API登陆了网站
*   用户在浏览你的网站时只保存了一个认证对象
*   用户在上一次访问时没有主动退出登录

如果任意条件不符合，这个函数便会被`reject`

![api_simple_login_07.png][7]


## 调解(Mediate)自动登录

当用户退出登录时，**应该由你来确保用户下次回访时不会自动登录 **。认证管理API提供了一种叫作**mediation（调解）**的机制来确保这一点。你可以通过调用[`navigator.credentials.requireUserMediation()`](https://developer.mozilla.org/en-US/docs/Web/API/CredentialsContainer/requireUserMediation)来开启调解模式。只要用户在这个网站中的调解状态为开启，那么如果你在`navigator.credentials.get()`方法中选择了`unmediate:true`，这个函数会在`resolve`时，传入`undefined`。

### 调解自动登录

```javascript
navigator.credentials.requireUserMediation();
```


![](https://developers.google.com/web/updates/images/2016/04/credential-management-api/image07.png)

# FAQ

** 在网页中，Javascript是否有可能获得原始密码？**
不能。密码只能作为`PasswordCredential`对象中的一部分，对外是不可访问的。

**Is it possible to store 3 set of digits for an id using Credential Management API?** 
** 我们可以通过认证管理API为一个id保存三组数字吗？ **
目前还不行。我们非常感谢你[对标准的反馈](https://github.com/w3c/webappsec-credential-management) 

** 我可以在iframe中使用认证管理API吗？
这个API只能在最高级的上下文中使用。如果在iframe中调用`.get()`或`.store()`方法，这些方法会马上`resolve`，并且不会产生任何效果。

** 我可以在我的密码管理Chrome扩展中集成认证管理API吗？
你可以重写`navigator.credentials`，并将它指向你的Chrome扩展，由扩展来`get()`或`store()`认证对象。



###### 作者

[Eiji Kitamura](http://blog.agektmr.com/ "Eiji Kitamura's Homepage")

Developer Advocate in Tokyo


  [1]: https://imgs.gnux.cn/usr/uploads/2016/06/177178324.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/06/3063293215.png
  [3]: https://imgs.gnux.cn/usr/uploads/2016/06/2706139368.png
  [4]: https://imgs.gnux.cn/usr/uploads/2016/06/2076238208.png
  [5]: https://imgs.gnux.cn/usr/uploads/2016/06/1703002568.png
  [6]: https://imgs.gnux.cn/usr/uploads/2016/06/291667656.png
  [7]: https://imgs.gnux.cn/usr/uploads/2016/06/2116612182.png