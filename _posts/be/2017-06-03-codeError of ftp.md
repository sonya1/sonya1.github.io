---
layout: post
title: 登陆FTP服务器代码实现
category: 后台
tags: C FTP服务器
description: 匿名登陆FTP服务器 发送命令的代码问题
---


socket进行connect连接，返回220代表成功。
发送USER，返回331。
发送PASS，返回220。
则成功。

代码：
```c
send(socket,"USER anonymous\r\n",strlen("USER anonymous\r\n"),0);//sizeof不可以
send(socket,"PASS anonymous\r\n",strlen("PASS anonymous\r\n"),0);
```
