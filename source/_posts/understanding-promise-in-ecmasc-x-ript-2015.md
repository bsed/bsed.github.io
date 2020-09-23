---
title: 如何理解JavaScript 的 Promise [JS基础增强]
date: 2019-03-24 13:30:00
updated: 2019-09-17 13:58:05
tags: 
- JS基础增强
- 箭头函数
categories: 
- js

---
ECMAScript6中新增了Promise对象， 所谓Promise对象，即代表着一个还未完成，但将来某时会完成的操作（通常是异步操作）。使用Promise对象，我们就可以避免陷入函数层层嵌套的‘回调金字塔’里去，将异步的操作以同步的的形式展现出来。Promise对象对外提供了一整套完整的接口，方便开发者更好地控制异步操作。

> Promise语法

ES6的Promise对象是一个构造函数，创建一个Promise对象实例的语法如下：
```javascript
new Promise (function(resolve, reject){});
```
Promise接受一个函数作为参数，这个函数也包含两个参数，这两个参数分别是resolve方法和reject方法，其中resolve方法在处理执行成功的时候将Promise对象的状态变为”fulfilled “，而reject方法则在处理执行失败的时候将Promise对象的状态变为”rejected”。
```javascript
var promise = new Promise(function(resolve,reject){
    if (/*异步操作成功*/) {
        resolve(value)
    } esle {
        reject(error)
    }
});

promise.then(function(value){//success},function(value){//error})
```


<!--more-->


从上面的代码我们可以看到，Promise对象实例生成后，可以使用then方法为异步操作成功或者失败的时候指定相应的处理方法。来看一个简单的例子。
```javascript
if (window.Promise) {
  var promise = new Promise(function(resolve,reject){
      var request;
      if (window.XMLHttpRequest) {
           request = new XMLHttpRequest();
       } else {
           request = new ActiveXObject();
       }
           request.open('GET','https://cnodejs.org/api/v1/topics');
           request.send();
           request.onload = function() {
               if(request.status == 200){
                    resolve(request.response)
                }else{
                     reject(Error(request.statusText));
                }
           }
           request.onerror = function() {
                  reject(Error('fetch data error.'))
           }

  })
 } else {
    console.log('Promise not support.');
 }

 promise.then(function(value){
        console.log(value);
  },function(error) {
        console.log(error.message);
  })
```
以上例子的代码即是Promise实现的异步请求。

> Promise概念

理解Promise之前，先来看看Promise对象包含的几种状态：

- ①pending:既不是fulfilled状态，也不是rejected状态
- ②fulfilled:成功的操作
- ③rejected:失败的操作

![es2015_promise.png][1]
从上面的流程图可以看出，处于pending状态的Promise对象既可以转变为一个带有成功值的fulfill状态，也可以转变为一个带有失败信息的rejected状态。当Promise的状态发生改变时，由Promise.then绑定的回调函数就会被调用。

Promise对象的原型：Promise.prototype

Promise.prototype即是Promise构造器的原型，所有的Promise实例对象都继承自Promise.prototype,可以通过Promise构造器的原型对象来给所有Promise实例添加属性和方法。

Promise.prototype.catch(onReject): catch方法只用来处理Promise被拒绝的情况，同时返回一个Promise对象，这个原型方法的行为和调用跟promise.then(undefined,onReject)相同。

promise.catch(onReject);
promise.then(function(error){//拒绝})

Promise.prototype.then(onfulfilled,onRejected):then方法也会返回一个Promise对象，then方法有两个参数，它们分别是Promise成功或者失败时的回调函数。

promise.then(onFulfilled,onRejected);
promise.then(function(value){
console.log(value)//成功状态
}，function(reason){
console.log(reason.message);//失败状态
})

## 链式Promise
由于Promise对象的catch方法和then方法都返回一个Promise对象，所以可以实现Promise的链式调用，可以帮助我们陷入“回调金字塔”，例如当你的某个操作返回数据，然后你要基于这些数据完成另一个操作，再返回新的数据，然后再基于这些新的数据完成另一个新的操作，这时我们就可以使用Promise对象的链式调用，以增强代码的可读性，同时从一层嵌套一层的“回调金字塔”中挣脱出来。还是以异步请求举个例子。
```javascript
function getData(url) {
    return new Promise(function(resolve,reject){
        var request;
        if (window.XMLHttpRequest) {
             request = new XMLHttpRequest();
         } else {
             request = new ActiveXObject();
         }
         request.open('GET',url);
         request.send();
         request.onload = function() {
             if(request.status == 200){
                  resolve(request.response)
              }else{
                   reject(Error(request.statusText));
              }
         }
         request.onerror = function() {
                reject(Error('fetch data error.'))
         }
    })
}

getData('https://cnodejs.org/api/v1/topics').then(function(result){
   var result = JSON.parse(result);
   return getData('https://cnodejs.org/user/'+result.data[0].author.loginname);//由于浏览器同源策略的原因，这个异步请求是失败的，这里只是示意
}).then(function(result){
   console.log(result);
}).catch(function(error){
   console.log(error);
});
```
> Promise的错误处理

通过前面的例子，我们也可以看到，Promise对象的错误由catch方法来捕获，传入catch方法的参数即为Promise发生错误时的回调函数。前面也提到了，catch方法的行为和调用跟Promise.prototype.then(undefined,rejected)相同，也就是说then方法里面传入的第二个参数rejected函数和传入catch方法的回调函数，都会在Promise对象状态变为”rejected”时被调用。

作者：xiaoweihuang
链接：https://www.xiaoweihuang.me/2018/11/05/2016-08-25-about-promise/


  [1]: https://imgs.gnux.cn/usr/uploads/2019/03/3907595312.png