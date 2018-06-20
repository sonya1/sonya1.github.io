---
layout: post
title: Calculator题
category: JS
tags: JS
description: 
---

### 笔试题 

实现如下代码

Calculator(10).add(4).reduce(3).time(2).divide(2).print();  //  11


```js
var Calculator = function(n) {
    return  new Calculator.prototype.init(n);
}

Calculator.prototype = {
  init: function(n) {
	  this.n = n;
    return this;
  },
   add:function(x){
		this.n += x;
		return this;
	},
	reduce:function(x){
		this.n -=x;
		return this;
	},
	time:function(x){
		this.n *= x;
		return this;
	},
	divide:function(x){
		this.n /= x;
		return this;
	},
	print:function(){
		console.log(this.n);
	}
}

Calculator.prototype.init.prototype = Calculator.prototype;

Calculator(10).add(4).reduce(3).time(2).divide(2).print();  //11

```

参考链接:[http://www.cnblogs.com/aaronjs/p/3278578.html](http://www.cnblogs.com/aaronjs/p/3278578.html)
