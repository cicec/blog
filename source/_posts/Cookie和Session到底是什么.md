---
title: Cookie和Session到底是什么
date: 2018-03-18 20:20:51
tags: 前端相关
---

## 前戏

阅读本文需要：

- 了解一点HTTP相关知识（请求和响应）。

废话：

- 真是越来越短了呢233。。

## 正文

### Cookie

当用户访问某个网站时，如果他已经登录了这个网站，那么在一定的时间内，都不需要重新进行登录了。因为在用户登录网站时，服务器就会发送给浏览器一小段数据，数据中记录部分用户相关的信息。而浏览器需要将这一小段数据保存在本地，并且每次访问相同域名的网站时，都需要在请求中带上这一段数据，而服务器就会根据这一段数据来查询访问用户的信息。

这段数据就被叫做Cookie。

就好像有的游玩景点会售卖一些包月票，那么游客只需要在第一次去景点的时候购买一张票，之后的一个月内，就不需要再重新买票了。如果游客想要进入景区游玩，就必须带上这张票。景区的工作人员会根据这张票检查游客的身份。

而游客访问景区的时候如果并没有买票（未登陆），那么他就会被提醒要么买票（登陆），要么就在门口看看得了（以游客身份访问）。

Cookie通过设置返回响应的响应头来设置。格式：

> Set-Cookie: cookie名=cookie值

而浏览器再次访问同一网站时就会自动添加一个Cookie的请求头：

> Cookie: cookie名=cookie值

这样服务器就能顺利查找到访问用户的信息了。

### Session

Cookie看起来似乎没什么问题，但实际上是很不安全的。因为Cookie可以被人为随意篡改，如果直接将用户的信息比如用户账号作为Cookie返回给浏览器，那么如果有人知道了其他人的账号，就可以通过直接修改Cookie来冒充那个人登录网站，从而窃取他的隐私信息。

这样就需要用到Session。

Session是保存在服务器的一组数据结构。每当服务器需要设置响应的Cookie时，就不会直接将用户的信息作为Cookie的值返回，而是将这段信息保存到Session中，再创建一个随机字符串作为这段信息的id，然后设置Cookie的值时只需要返回这个Session的id值就好了。当用户再次访问网站时，就通过Session的id在服务器保存的Session数据中找到对应的用户信息。

### Cookie和Session的联系和区别

- Cookie是客户端向服务器发起请求时，服务器发送给客户端的一段数据，用于记录用户的一些信息，以便区分访问的用户。
- Session是在服务端保存的一个数据结构，用来跟踪用户的状态。
- Cookie保存在客户端，Session保存在服务器端。
- 一般来说，Session是基于Cookie实现的。服务器会把Session的Id通过Cookie发送给浏览器，浏览器访问服务器时再通过Cookie中的sessionId查找服务器中的Session。

## 结语

- 写结语了吗？
- 没有哦。