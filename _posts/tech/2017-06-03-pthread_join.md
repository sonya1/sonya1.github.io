---
layout: post
title: pthread_join的问题_sonya
category: 技术
tags: centOS linux
description: 
---


新建线程 没执行，就退出了？？？

因为主线程退出了，所以新建的线程没等执行完就退出了。
主线程等待新建线程退出，pthread_join()函数。


![tu](https://sonya1.github.io/assets/img/blog/thread_join.png)
