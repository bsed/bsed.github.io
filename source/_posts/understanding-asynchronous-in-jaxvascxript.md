---
title: 理解JavaScript 中的 asynchronous
date: 2017-08-07 08:20:00
updated: 2018-12-27 08:55:40
tags: 
- weex
categories: 
- android

---
This article is part of a web development series from Microsoft. Thank you for supporting the partners who make SitePoint possible.

JavaScript has come a long way since its early versions and thanks to all efforts done by TC39 (The organization in charge of standardizing JavaScript (or ECMAScript to be exact) we now have a modern language that is used widely.

One area within ECMAScript that received vast improvements is asynchronous code. You can learn more about asynchronous programming here if you’re a new developer. Fortunately we’ve included these changes in Windows 10’s new Edge browser—check out the Microsoft Edge change log.

Among all these new features, let’s specifically focus on “ES2016 Async Functions” behind the Experimental Javascript features flag and take a journey through the updates and see how ECMAScript can improve your currently workflow.


<!--more-->


JavaScript很早就出现在计算机历史，让我们感谢TC39为我们所做的工作（定义JavaScript标准的组织，或者称为ECMAScript），我们拥有了被广泛使用的现代编程语言。

### First stop: ECMAScript 5 – Callbacks city

ECMAScript 5 (and previous versions as well) are all about callbacks. To better picture this, let’s have a simple example that you certainly use more than once a day: executing a XHR request.

ECMAScript 5 (或者是以前的版本)几乎都是回调函数。为了更好的说明，我们来看一个简单的例子：

```
var displayDiv = document.getElementById("displayDiv");

// Part 1 - Defining what do we want to do with the result
// 第一部分： 定义当结果返回是的处理方法

var processJSON = function (json) {
	var result = JSON.parse(json);
   result.collection.forEach(function(card) {
	var div = document.createElement("div");
   div.innerHTML = card.name + " cost is " + card.price;
        displayDiv.appendChild(div);
    });
}

// Part 2 - Providing a function to display errors
//第二部分： 出现错误时的显示操作。
var displayError = function(error) {
    displayDiv.innerHTML = error;
}

// Part 3 - Creating and setting up the XHR object
// 第三部分： 建立和启动一个XHR对象。
var xhr = new XMLHttpRequest();
xhr.open('GET', "cards.json");

// Part 4 - Defining callbacks that XHR object will call for us
//第四部分： 定义XHR对象的回调函数。 

xhr.onload = function(){
if (xhr.status === 200) {
        processJSON(xhr.response);
    }
}

xhr.onerror = function() {
    displayError("Unable to load RSS");
}

// Part 5 - Starting the process
//第五部分： 启动进程。
xhr.send();
```

Established JavaScript developers will note how familiar this looks since XHR callbacks are used all the time! It’s simple and fairly straight forward: the developer creates an XHR request and then provides the callback for the specified XHR object.

In contrast, callback complexity comes from the execution order which is not linear due to the inner nature of asynchronous code:

经验丰富的JavaScript程序员对于XHR的异步回调操作非常熟悉！这个很简单也很直接： 开发者建立一个XHR请求，然后给XHR对象提供一些回调函数。

相反，回调函数的复杂性来自于其非线性执行的异步代码：

![ECMAScript 5 XHR request callback](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/10/1445201763asynchronous-javascript01-es5-xhr-request-callback.png)

The [“callbacks hell”](https://callbackhell.com/) can even be worse when using another asynchronous call inside of your own callback.

当你在回调函数中调用另一个回调函数时，“callbacks hell” 会更加严重。

### Second stop: ECMAScript 6 – Promises city

ECMAScript 6 is gaining momentum and Edge is has leading support with 88% coverage so far.

Among a lot of great improvements, ECMAScript 6 standardizes the usage of promises (formerly known as futures).

According to MDN, a promise is an object which is used for deferred and asynchronous computations. A promise represents an operation that hasn’t completed yet, but is expected in the future. Promises are a way of organizing asynchronous operations in such a way that they appear synchronous. Exactly what we need for our XHR example.

Promises have been around for a while but the good news is that now you don’t need any library anymore as they are provided by the browser.

Let’s update our example a bit to support promises and see how it could improve the readability and maintainability of our code:

ECMAScript 6正在继续动力，并且Edge已经对ECMAScript有88%的支持。

在这些众多的改进中，ECMAScript 6对Promise做标准化。根据MDN的解释，promise就是用来延迟和异步执行的对象。Promise代表未完成的操作会在未来完成。Promise就是通过同步的展现形式来处理异步的操作。这正是我在XHR所需要的。

```
var displayDiv = document.getElementById("displayDiv");

// Part 1 - Create a function that returns a promise
//第一部分： 创建一个返回promise 的函数
function getJsonAsync(url) {
	// Promises require two functions: one for success, one for failure
	return new Promise(function (resolve, reject) {
		var xhr = new XMLHttpRequest();
       xhr.open('GET', url);
       xhr.onload = () => {
			if (xhr.status === 200) {
				// We can resolve the promise
				resolve(xhr.response);
          } else {
				// It's a failure, so let's reject the promise
				reject("Unable to load RSS");
          }
       }

       xhr.onerror = () => {
			// It's a failure, so let's reject the promise
			reject("Unable to load RSS");
       };

       xhr.send();
    });
}

// Part 2 - The function returns a promise
// so we can chain with a .then and a .catch
//第二部分：函数返回一个Promise
//这样我们就可以使用链式的使用.chain, .then
getJsonAsync("cards.json").then(json => {
	var result = JSON.parse(json);
   result.collection.forEach(card => {
		var div = document.createElement("div");
       div.innerHTML = `${card.name} cost is ${card.price}`;
       displayDiv.appendChild(div);
   });
}).catch(error => {
    displayDiv.innerHTML = error;
});
```

You may have noticed a lot of improvements here. Let’s have a closer look.

#### Creating the promise

In order to “promisify” (sorry but I’m French so I’m allowed to invent new words) the old XHR object, you need to create a Promise object:

首先你需要创建一个Promise对象。

![ECMAScript 6 creating Promise Object](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/10/1445201770asynchronous-javascript02-es6-creating-promise-object.png)

#### Using the promise
Once created, the promise can be used to chain asynchronous calls in a more elegant way:

当我们创建对象之后，promise可以更加优雅的被用来链接异步回调操作。 

![Using Promise Object to chain asynchronous calls](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/10/1445201777asynchronous-javascript03-using-promise-to-chain-asynchronous-calls.png)

So now we have (from the user standpoint):

* Get the promise (1)
* Chain with the success code (2 and 3)
* Chain with the error code (4) like in a try/catch block

What’s interesting is that chaining promises are easily called using .then().then(), etc.

Side node: Since JavaScript is a modern language, you may notice that I’ve also used syntax sugar from ECMAScript 6 like template strings or arrow functions.

现在我们就可以： 
* 获取promise对象（1）
* 链接请求成功的处理代码（2，3）
* 像使用try/cacht的方式来链接错误处理（4）

这里我们只需要使用`.then().then()`就可以实现promise 的链式调用。 

这里我们已经使用了ECMAScript 6中的模板字符串和箭头函数。

### Terminus: ECMAScript 7 – Asynchronous city

Finally, we’ve reached our destination! We are almost in the future, but thanks to Edge’s rapid development cycle, the team is able to introduce a bit of ECMAScript 7 with async functions in the latest build!

Async functions are a syntax sugar to improve the language-level model for writing asynchronous code.

Async functions are built on top of ECMAScript 6 features like generators. Indeed, generators can be used jointly with promises to produce the same results but with much more user code.

We do not need to change the function which generates the promise as async functions work directly with promise.

We only need to change the calling function:

最后，我们到达了终点.

Async 函数是一个在语言层面改写异步代码的语法糖。Async函数是建立在ECMAScript 6新特性（如generator）的基础之上的。确实，使用generator和Promise确实能够实现相同的代码特性，只是代码会更多一些。


```
// Let's create an async anonymous function
(async function() {
try {
// Just have to await the promise!
var json = await getJsonAsync("cards.json");
	var result = JSON.parse(json);
   result.collection.forEach(card => {
		var div = document.createElement("div");
       div.innerHTML = `${card.name} cost is ${card.price}`;
       displayDiv.appendChild(div);
    });
} catch (e) {
        displayDiv.innerHTML = e;
}
})();
```

This is where magic happens. This code looks like a regular synchronous code with a perfectly linear execution path:

这的代码看起来就是正常的同步代码并有完美的线性执行路径。 

[Creating async function](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2015/10/1445201783asynchronous-javascript04-creating-async-function.png)

Quite impressive, right?

And the good news is that you can even use async functions with arrow functions or class methods.

真是印象深刻哦？ 

将来你甚至可以在async 函数中使用箭头函数或者`class`方法。

Going further
If you want more detail on how we implemented it in Chakra, please check the official post on the Microsoft Edge blog.You can also track the progress of various browsers implementation of ECMAScript 6 and 7 using Kangax’s website.

Feel free also to check our JavaScript roadmap as well! Please, do not hesitate to give us your feedback and support your favorite features by using the vote button:

JavaScript Roadmap vote button

Thanks for reading and we’re eager to hear your feedback and ideas!

原文：[Javascript goes asynchronous](https://www.sitepoint.com/javascript-goes-asynchronous-awesome/)
