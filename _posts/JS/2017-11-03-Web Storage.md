---
layout: post
title: cookie、session、webStorage的区别
category: JS
tags: Storage
keywords: Storage
description: Storage
---

## session与cookie的区别

1.session在服务器端使用一种类似散列表的结构保存信息，客户端不知道其中的信息。cookie保存在浏览器端，服务器能知道其中的信息。

2.session的运行依赖sessionID，而sessionID是存在cookie中。（也有其他选择，如URL重写，将sessionID放在URl中等等）

3.session可以存放在文件、数据库、内存中。

4.cookie不是很安全，别人可以分析存放在本地的cookie并进行窃取，考虑到安全应当使用session。

5.session会在一定时间内保存在服务器上，当访问增多，会比较占用服务器的性能，考虑到减轻服务器负担，应当使用cookie。

6.单个cookie保存的数据不超过4K，很多浏览器都限制一个站点最多保存20个，而session数量没有限制。

## Cookie，LocalStorage，SessionStorage的区别（都是）

cookie是网站为了标识用户身份而存储在用户本地终端的数据。会在客户端和服务器端来回传送。

sessionStorage和localStorage是html5提供的存储方式，不会自动把数据发送给服务器，仅在本地保存。

### 存储大小
cookie数据大小不能超过4K

sessionStorage和localStorage虽然存储大小也有限制，但比cookie大得多，可以达到5M或更多。

### 有效时间
cookie 在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。不设过期时间的话，关闭浏览器就消失。

localStorage 存储持久数据，浏览器关闭后数据不丢失。除非手动删除数据，否则将一直保留。

sessionStorage 数据在当前浏览器窗口关闭后自动删除。维持一个会话的时间。

### 作用域不同
cookie的内容主要包括：名字，值，过期时间，路径和域。路径和域一起构成cookie的作用域范围。

cookie 作用域一般是同源窗口中都共享。是通过path路径来确定的，当path为“/”时，就是所有同源窗口都共享。即使来自不同的浏览器。

localStorage 在所有同源窗口中都是共享的。即使来自不同的浏览器。

sessionStorage 无法在不同的浏览器窗口中共享，即使是同一个页面。

## 个数限制
每个域名下的cookie的个数会有限制，依据浏览器不同会有不同，而localStorage数量是无限制的。



