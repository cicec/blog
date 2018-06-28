---
title: 浅谈：使用CORS实现跨域请求
date: 2018-06-28 16:18:45
tags: 前端相关
---

## 前戏

- 之前写博客的时候还提到过CORS，可是没有认真研究过。。。
- 正好今天踩坑了，就好好记录一下。

## 正文

### 简单请求

CORS（跨域资源共享），是一个针对跨域HTTP请求制定的标准。解决了以往 XMLHttpRequest 请求只能同源访问的限制。

要使用CORS，需要浏览器和服务器同时支持。也就是说，需要服务器返回响应时额外添加响应头，使CORS生效。

而浏览器端的CORS由浏览器自己完成，当浏览器发现请求指向不同源的地址时，就会自动添加CORS所必要的信息。

浏览器会根据请求类型的不同将请求分为两类：简单请求和非简单请求。判断的规则如下：

``` text
1. 请求方法是以下三种方法之一：
  HEAD
  GET
  POST
2. HTTP的头信息不超出以下几种字段：
  Accept
  Accept-Language
  Content-Language
  Last-Event-ID
  Content-Type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain
```

满足以上两个条件，那么浏览器就视此次请求为简单请求。即直接在这次请求的请求头中添加一个 Origin 字段，内容为请求方的域名信息。

请求信息：

``` http
POST /signin HTTP/1.1
Host: localhost:8080
Connection: keep-alive
Content-Length: 25
Origin: http://localhost:3000
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.132 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Accept: */*
Referer: http://localhost:3000/
Accept-Encoding: gzip, deflate, br
Accept-Language: zh,en;q=0.9,ja;q=0.8
```

那么服务器收到请求后，只需要告知浏览器是否允许 Origin 字段指定的域名进行请求。于是就要在响应信息中添加 Access-Control-Allow-Origin 字段，内容为允许请求的域名（或直接设置为*，表示允许任意域名请求）。例如：

``` http
Access-Control-Allow-Origin: http://localhost:3000
```

除了Access-Control-Allow-Origin，还有CORS还提供了几个可选的字段：

- Access-Control-Allow-Credentials：表示是否允许发送Cookie，默认不允许，可设置为true允许发送。

- Access-Control-Expose-Headers：用于设置浏览器接收到响应时可以拿到的响应头信息。默认可以拿到 Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma 六个基本字段。

### 非简单请求

那么在很多时候，只使用简单请求提供的几个可选项是不够的。例如当浏览器想向服务器提交一段json格式的数据，即需要将 Content-Type 字段设置为 application/json 时，这个请求就会被浏览器判定为非简单请求。这时浏览器不会直接发送原请求，而是会首先发送一个 OPTIONS 类型的预检请求，用于判断原请求是否被允许。请求信息如下：

``` http
OPTIONS /signin HTTP/1.1
Host: localhost:8080
Connection: keep-alive
Access-Control-Request-Method: POST
Origin: http://localhost:3000
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.132 Safari/537.36
Access-Control-Request-Headers: content-type
Accept: */*
Referer: http://localhost:3000/
Accept-Encoding: gzip, deflate, br
Accept-Language: zh,en;q=0.9,ja;q=0.8
```

这段请求信息包含着两个字段：Access-Control-Request-Method 和 Access-Control-Request-Headers。分别代表此次原请求的方式和请求指定的头信息。而服务器则需要返回允许请求的方式和可携带的请求头，通过 Access-Control-Allow-Methods 和 Access-Control-Allow-Headers 字段指定。

这里以node.js（express）为例：

``` js
router.use((req, res, next) => {
    res.header('Access-Control-Allow-Origin', '*')

    if (req.method === 'OPTIONS') {
        res.header('Access-Control-Allow-Methods', 'GET, POST')
        res.header('Access-Control-Allow-Headers', 'Content-Type')
    }
    next()
})
```

其中 Access-Control-Allow-Origin 字段是每次响应都必须要包含的。

除了上面这3个字段，非简单请求还有着两个可选的字段：

- Access-Control-Allow-Credentials：与上文相同，表示是否允许发送Cookie。

- Access-Control-Max-Age：用于指定本次预检请求的有效期，以秒为单位。

当浏览器接收到本次预检请求的响应结果后，就会判断服务器是否允许这次请求。若允许，就直接再次发送原请求并接收响应结果；若不允许，则会在控制台输出错误信息。

请求成功后，在上一次预检请求的有效期内，都不需要再次发送预检请求了，而是直接被当做简单请求来处理。

## 结语

参考文档：

- [跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)
- [HTTP访问控制（CORS）](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)

