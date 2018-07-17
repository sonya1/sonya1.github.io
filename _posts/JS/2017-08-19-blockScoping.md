---
layout: post
title: 块作用域--《你不知道的JS 上》
category: JS
keywords: 块作用域
---

```js
function foo(){
	var val = "hello";

	function bar(){
		function baz(){
			window.val = "world";
		}
		baz();
		console.log("val-->",val);  //hello
	}
	bar();
}
foo();
```
bar里面输出val，先在当前作用域中查找，没找到，就去foo里面，找到了，输出hello。baz里面定义的是全局变量，所以还没找到全局就在foo里找到了。

JS中块作用域的例子：with,try/catch，let，const。

```javascript
//无块作用域 
for(var i=0;i<10;i++){
	console.log(i);
}
console.log(i);  //10
//在C语言等支持块作用域的语言中，会报错，
```
