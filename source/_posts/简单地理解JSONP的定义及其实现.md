---
title: 简单地理解JSONP的定义及其实现
date: 2018-03-04 13:31:00
tags: 前端相关
---

## 前戏

- 写的很详细了，应该很好懂。
- 注：文中代码均是截取的代码片段，仅供参考和理解，酌情复制。

## 正文

### 同源策略

同源策略规定只在协议相同、域名相同、端口相同的情况下，也就是两个网页同源时，才能读写对方的资源。这是为了保证用户的信息安全做出的限制，然而同源策略有时也会对合理的用途造成影响，那么就需要想办法规避同源策略带来的影响。

### 用script标签发请求

浏览器解析html页面时，如果看到有如link、img、script等标签时，就会自动向标签对应路径发起一个get请求以获取想要得到的资源。而用标签发起的请求是不受同源策略限制的，利用这种特性，就可以达到规避同源策略向目标网站发起请求的目的。

例如，页面中有一个script标签：

``` html
<script src="http://demo.com/test"></script>
```

这个标签就会向 demo.com 下的 test 路径发起请求，而当服务器接收到请求后就会做出响应。当然一般这种标签是通过JavaScript来动态创建的：

``` js
const script = document.createElement('script')
script.src = 'http://demo.com/test'
document.body.appendChild(script)
```

这样就能直接用js动态地发送请求了。需要注意的是，因为请求是通过script发送的，所以浏览器接收到响应时会立即执行请求到的结果。例如：

``` js
...
if (pathNoQuery === '/test') {
    response.setHeader('Content-Type', 'application/javascript')
    response.write('alert('这是 test 路径返回的结果。')')
} else {
    response.statusCode = 404
}
response.end()
...
```

这里服务器接收到请求后返回了 `alert(...)` 的代码，浏览器接收到结果后就会执行 `alert(...)`，也就会弹出一个提示框。利用这一点，就可以直接在接收到的script标签中使用得到的数据。假设页面中就一个id为balanceText的元素用来显示用户的余额，服务器通过getResultFromDb函数来获取数据库中的数据：

``` js
...
if (pathNoQuery === '/test') {
    const rs = getResultFromDb()
    response.setHeader('Content-Type', 'application/javascript')
    response.write(`balanceText.textContent = '你还有${rs.balance}块钱'`)
} else {
    response.statusCode = 404
}
response.end()
...
```

这样每次向服务器请求时，服务器就会取得数据库中的数据并返回更改对应元素文本的执行语句，浏览器接收响应后执行语句，即可在页面中显示出接收到的数据。

然而这种方法很令人困惑，展示数据明明应该是前端应该做的事，却被后端做了。而且后端为了展示数据还需要了解前端的页面结构。而这也导致后端与前端的耦合度太高，后端的响应几乎只能为这一个页面服务。

那么解决这个问题的思路自然是降低前后端的耦合度，也就是解耦。前端的代码就应该放到前端去写，而后端只需要将数据交给前端就好，不需要做额外的事情。

### 回调函数

解决的办法是在前端代码中定义一个函数，也就是所谓的回调函数。服务器返回的代码中只需要执行这个函数，而前端想要获取的数据只需要通过参数传递到这个函数中即可。这里就可以修改一下之前的代码：

前端：

``` js
const cbFunc = (result) => {
    balanceText.textContent = `你还有${result}块钱`
}
const jsonpScript = document.createElement('script')
jsonpScript.src = 'http://demo.com/test?callback=cbFunc'
document.body.appendChild(jsonpScript)
```

后端：

``` js
...
if (pathNoQuery === '/test') {
    const rs = getResultFromDb()
    response.setHeader('Content-Type', 'application/javascript')
    response.write(`${queryObject.callback}(${rs.balance})`)
} else {
    response.statusCode = 404
}
response.end()
...
```

这里前端请求时在查询参数中传递了一个callback参数，记录回调函数的函数名。后端只需要使用这个函数名返回一个调用回调函数的语句就好了。与此同时请求的数据也作为参数被传递回给回调函数，接着只需要在回调函数中使用这个参数就可以得到想要的数据了。这样后端就只需要专注于返回数据而无需操心前端的代码，前端拿到数据也就可以放心地为所欲为了。

### JSONP

那么至此为止，上文说了这么多，和JSONP有什么关系？

在这里总结一下：

1. 动态创建地创建script标签以发起请求，在src中填写请求的目标路径，并传入查询参数callback也就是回调函数的函数名。
2. 服务器接收到请求时，会根据查询参数callback返回执行回调函数的语句，并在参数传入请求方想要的数据。
3. 请求方接收到响应后就会执行这个语句也就是执行回调函数，这样请求方就能在回调函数中取得想要的数据。

这个请求的过程就是JSONP。

## 结语

- 所以说JSONP和JSON其实没啥关系，只是JSONP一般用来传输JSON。
- JSONP和AJAX也没什么关系。
- JSONP不能发起POST请求，因为script标签本身无法发POST请求。