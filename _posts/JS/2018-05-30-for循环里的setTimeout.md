---
layout: post
title: for循环里的setTimeout异步问题
category: JS
tags: for循环里的setTimeout
keywords: for循环里的setTimeout
description: for循环里的setTimeout
---

```javascript
 
 for(var i=0;i<10;i++){
  setTimeout(function(){
    console.log(i);
  });
 }

//输出10个 10
```

因为setTimeout函数是异步的，异步函数必须等待主进程运行完毕才会运行，setTimeout内部回调运行的时候，主进程已经运行完了，i=10.所以会输出
10个10。

循环一次 就将settimeout这个异步函数放到任务队列（存放异步函数的队列），然后执行主进程的任务，这样就有10个setTimeout函数。
当主进程运行完时，i=10.然后从任务队列中取出函数放到主线程中运行，运行10次，输出10个10.
