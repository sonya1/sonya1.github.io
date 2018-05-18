---
layout: post
title: sizeof和strlen的区别
category: 后台
tags: C 
description: 
---


### sizeof和strlen的区别      

 在Windows下：
```c
char *str = "12345678\r\n";
printf("sizeof-->%d\n",sizeof(str));  //4  是字符指针的数据类型长度 
printf("strlen-->%d\n",strlen(str));  //10 就是str的长度

```
