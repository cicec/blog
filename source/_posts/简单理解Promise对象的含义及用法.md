---
title: 简单理解Promise对象的含义及用法
date: 2018-06-26 13:03:01
tags: 前端相关
---

## 前戏

- 临时复习顺便整理一下，可能有些错误和遗漏。
- 最近把Promise的实现也补上吧。

## 正文

### Promise的含义及用法

Promise是ES6提供的新标准，用于处理异步编程。在JavaScript中，Promise是一个构造函数，用来创建Promise对象。

Promise构造函数接收一个函数作为参数，这个函数内部通常是一个异步操作。Promise对象有三种状态，分别是pending（初始状态）、fulfilled（成功）和rejected（失败）。也就是说当作为参数的这个异步操作函数执行成功时，就将Promise对象状态改变为fulfilled。反之，如果失败就将Promise对象的状态改变为rejected。

那么作为参数传入Promise的函数就拥有着两个参数，即resolve和reject，分别用于将Promise对象的状态更改为fulfilled（完成）或rejected（失败）。

这里使用Promise对象来实现一个ajax函数：

``` js
$.ajax = ({ url, method, body, headers }) => new Promise((resolve, reject) => {
    const request = new XMLHttpRequest()
    request.open(method, url)
    Object.keys(headers).forEach((key) => {
        request.setRequestHeader(key, headers[key])
    })
    request.onreadystatechange = () => {
        if (request.readyState === 4) {
            if (request.status >= 200 && request.status < 300) {
                //  请求成功，执行reslove
                resolve(request.responseText)
            } else if (request >= 400) {
                //  请求失败，执行reject
                reject(request)
            }
        }
    }
    request.send(body)
})
```

这个函数会返回一个Promise对象，当Promise对象被创建以后，就可以通过then方法绑定Promise对象状态变为fulfilled和rejected时的回调函数。当Promise对象的状态变成fulfilled或rejected也就是异步操作执行成功或失败后，就会调用then方法绑定的处理方法。then方法有两个参数，分别是onfulfilled方法和onrejected方法。异步操作执行成功时，调用then的onfulfilled方法，异步操作执行失败时，调用then的onrejected方法。

这里就可以使用一下刚刚实现的ajax函数：

``` js
$.ajax({
    url: '/login',
    method: 'post',
    body: 'username=cc&password=000'
}).then((result) => {
    //  请求成功
    console.log(result)
}, (request) => {
    //  请求失败
    console.log(request)
})
```

### 链式调用

Promise的then方法会返回一个新的Promise对象，这个Promise对象同样可以使用then方法，也就是说可以在上一个then方法后面直接再接一个then方法，这样就形成了链式调用。下一个then方法中指定的函数会接收上一个then方法中被执行的函数的返回值作为参数。如：

``` js
$.ajax({
    url: '/login',
    method: 'post',
    body: 'username=cc&password=000'
}).then((result) => {
    console.log(result)
    return doSomething(result)
}).then((newResult) => {
    //  这里输出的是上面的then函数中返回的被处理过的result
    console.log(newResult)
    //  同样可以再对这个result进行处理并传递给下一个then方法
    return doElseSomething(newResult)
})
```

## 结语

参考文档：

- [MDN：使用 promises](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_promises)
- [ECMAScript 6 入门：Promise对象](http://es6.ruanyifeng.com/#docs/promise)

