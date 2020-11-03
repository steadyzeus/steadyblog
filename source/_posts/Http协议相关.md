title: Http协议相关
author: Steady
date: 2020-05-12 17:16:40
tags:
---


1.强缓存离不开两个响应头Expires与Cache-Control

Expires：Expires是http1.0提出的一个表示资源过期时间的header，它描述的是一个绝对时间，由服务器返回，Expires 受限于本地时间，如果修改了本地时间，可能会造成缓存失效
Expires: Wed, 11 May 2018 07:20:00 GMT

Cache-Control: Cache-Control 出现于 HTTP / 1.1，优先级高于 Expires ,表示的是相对时间
Cache-Control: max-age=315360000


2.协商缓存
协商缓存就是强制缓存失效后，浏览器携带缓存标识向服务器发起请求，由服务器根据缓存标识决定是否使用缓存的过程，主要有以下两种情况：

协商缓存生效，返回304和Not Modified
协商缓存失效，返回200和请求结果

协商缓存可以通过设置两种 HTTP Header 实现：Last-Modified 和 ETag 。