---
title: 实现AJAX以及常见的跨域处理
date: 2018-03-11 17:08:46
tags: 前端相关
---

## 前戏

- 实现一个jQuery中的ajax函数。
- 简单介绍常见的跨域方法。

## 正文

### 实现AJAX

AJAX，翻译为中文叫做异步JavaScript和XML。然而这个名字反而会令人困惑，因此可以直接将AJAX理解为用JavaScript发请求。相较于传统的HTML发请求每次请求后都需要刷新整个页面，使用JavaScript发请求就可以实现请求后只对局部页面进行刷新，这样就能做到从前完全无法实现的功能，也让网页看起来更像一个程序了。

要使用AJAX发送请求，就需要创建一个XMLHttpRequest对象，这个对象为客户端提供了在客户端和服务器之间传输数据的功能：

``` js
const request = new XMLHttpRequest()
```

之后需要配置这个对象的请求方式和路径，这里要使用XMLHttpRequest对象的open方法：

``` js
request.open('post', '/login')
```

如果想要添加或自行设置请求头，可以使用serHeader方法：

``` js
request.setRequestHeader('haha', 'haha')
```

接下来就可以发送请求了，使用send方法，可以传递一个参数作为请求的内容也就是第四部分：

``` js
request.send('username=cc&password=000')
```

这样一个请求就已经发送出去了，当然只是这样的话并不能知道请求是否成功，以及请求什么时候完成，所以需要对XMLHttpRequest对象的状态变化进行监听（XMLHttpRequest对象有0-4共5种状态，其中状态为4时表示请求已经完成）：

``` js
request.onreadystatechange = () => {
    if (request.readyState === 4) {
        if (request.status >= 200 && request.status < 300) {
            console.log(request.responseText)
        } else if (request >= 400) {
            console.log(request)
        }
    }
}
```

这里监听当XMLHttpRequest对象的状态为4时，判断HTTP状态码是否在200-299之间，如果是则表示请求成功，否则请求失败。其中XMLHttpRequest对象的responseText属性的值就是响应的结果也就是第四部分。这样请求的结果就获取到了，并且可以使用结果进行相应的操作。

这样一个完整的请求就完成了，贴一下完整代码（）：

``` js
const request = new XMLHttpRequest()
request.onreadystatechange = () => {
    if (request.readyState === 4) {
        if (request.status >= 200 && request.status < 300) {
            console.log(request.responseText)
        } else if (request >= 400) {
            console.log(request)
        }
    }
}
request.open('post', '/login')
request.setRequestHeader('haha', 'haha')
request.send('username=cc&password=000')
```

那么要将这些操作封装成函数，只需要将其中需要自定义的部分作为参数传入就好了：

``` js
const ajax = (url, method, body, headers, success, fail) => {
    const request = new XMLHttpRequest()
    request.onreadystatechange = () => {
        if (request.readyState === 4) {
            if (request.status >= 200 && request.status < 300) {
                success(request.responseText)
            } else if (request >= 400) {
                fail(request)
            }
        }
    }
    request.open(method, url)
    Object.keys(headers).forEach((key) => {
        request.setRequestHeader(key, headers[key])
    })
    request.send(body)
}
```

这样看起来还不错，但还有些问题。首先每一个参数都是必传的，即使不想传递这个参数也需要传递一个null来占位，否则参数就会出现错误。为了解决这个问题可以将所有参数放到一个对象中一起传递进去。其次可以使用Promise对象来调用回调函数，来解决只能传递一个回调函数的问题：

``` js
const ajax = ({ url, method, body, headers }) => new Promise((resolve, reject) => {
    const request = new XMLHttpRequest()
    request.open(method, url)
    Object.keys(headers).forEach((key) => {
        request.setRequestHeader(key, headers[key])
    })
    request.onreadystatechange = () => {
        if (request.readyState === 4) {
            if (request.status >= 200 && request.status < 300) {
                resolve(request.responseText)
            } else if (request >= 400) {
                reject(request)
            }
        }
    }
    request.send(body)
})
```

OK，到这里这个ajax函数就基本完成了。

### 跨域处理

跨域的一种方法是使用jsonp来发送请求，利用标签不受同源策略限制的特点来实现跨域请求。具体实现可以查看之前的文章 [简单地理解JSONP的定义及其实现](http://clancy.me/2018/03/04/%E7%AE%80%E5%8D%95%E5%9C%B0%E7%90%86%E8%A7%A3JSONP%E7%9A%84%E5%AE%9A%E4%B9%89%E5%8F%8A%E5%85%B6%E5%AE%9E%E7%8E%B0/)

另一种方法便是使用CORS进行跨域，使用的方法十分简单，只需要在后端添加响应头Access-Control-Allow-Origin，并将值设置为请求的网站地址，即可实现跨域：

``` js
response.setHeader('Access-Control-Allow-Origin', 'http://demo.com')
```

## 结语

没有结语了呀。