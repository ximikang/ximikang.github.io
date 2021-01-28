---
title: fetch请求中的跨域和携带Cookies问题
date: 2021-01-14 00:00:00
tags: 
    - fetch
    - 跨域
    - 前端
---

## fetch解读
Fetch API 提供了一个 JavaScript 接口，用于访问和操纵 HTTP 管道的一些具体部分，例如请求和响应。它还提供了一个全局 fetch() 方法，该方法提供了一种简单，合理的方式来跨网络异步获取资源。这种功能以前是使用 XMLHttpRequest 实现的。Fetch 提供了一个更理想的替代方案，可以很容易地被其他技术使用，例如Service Workers。

## [问题]fetch请求中的跨域和携带Cookies问题
在有些请求中会遇到希望通过请求来访问不同源的api，并且希望携带cookies。

## 解决跨域问题
fetch可以设置不同的模式使得请求有效. 模式可在fetch方法的第二个参数对象中定义.
```javascript
fetch(url, {mode: 'cors'});
```
可以定义的模式如下：
- same-origin:表示同源可以进行访问，反之浏览器拒绝
- cors: 表示同源和带有cors响应头的跨域可以请求成功，其他拒绝
- cors-with-forced-preflight: 表示在发出请求前, 将执行preflight检查.
- no-cors: 表示跨域请求不带cors响应头场景，此时的相应类型为opaque，但是在opaque的返回类型中，我们几乎不能查看到任何有价值的信息，比如不能查看response, status, url。

## 解决跨域携带cookies问题
跨域请求中需要带有cookie时, 可在fetch方法的第二个参数对象中添加credentials属性, 并将值设置为”include”.
```javascript
fetch(url,{
  credentials: 'include'
});
```
除此之外, credentials 还可以取以下值:
- omit: 缺省值, 默认为该值.
- same-origin: 同源, 表示同域请求才发送cookie.

**但是同时需要目标服务器可以接受接受跨域发送cookies请求，否则会被浏览器的同源策略阻挡**
服务器端需要支持Access-Control-Allow-Credentials策略,服务器同时设置`Access-Control-Allow-Credentials`响应头为`"true"`， 即可允许跨域请求携带 Cookie。