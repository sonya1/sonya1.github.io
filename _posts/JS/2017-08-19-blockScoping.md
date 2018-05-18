---
layout: post
title: 块作用域--《你不知道的JS 上》
category: JS
keywords: 块作用域
---

JS中块作用域的例子：with,try/catch，let，const。

```
//无块作用域 
		for(var i=0;i<10;i++){
			console.log(i);
		}
		console.log(i);  //10
	//在C语言等支持块作用域的语言中，会报错，找不到外边的i
```


```
//with 
		function foo(obj){
			with(obj){
				a=2;
			}
		}
		var o1 = {
			a:3
		};
		var o2 = {
			b:3
		}
		foo(o1);
		console.log(o1.a); //2

		foo(o2);
		console.log(o2.a); // undefined  LRS引用 没找到obj2.a，所以放到全局了
		console.log(a); //2 --泄露到全局作用域了
```


```
//catch 
		try{
			undefined();  //执行一个非法操作来强制制造一个异常
		}catch(err){
			console.log(err); //能够正常执行  所以块作用域
		}
		console.log(err); //报错
```

```
//let
		var foo = true;
		if(foo){
			let bar = foo *2;
			bar =something(bar);
			console.log(bar);
		}
		console.log(bar);  //报错

		//但是需要注意 let声明 不会提升
		{
			console.log(bar);
			var bar = 1;  //var 声明会提升 undefined
			//let bar = 1;  //报错
		}
```

### let

1.垃圾收集
```
		//由于click回调形成闭包，所以process执行完毕，本该释放的bigData现在极有可能不会被释放。
		function process(data){
			// do something
		}
		var someReallyBigData = {};

		process( someReallyBigData );
		var btn = document.getElementById("btn");
		btn.addEventListener("click",function click(evt){
			console.log("button clicked");
		},/*capturingPhase*/false);

		//所以，使用块作用域可以打消这种顾虑
		function process(data){
			// do something
		}
		{
			let someReallyBigData = {};
			process( someReallyBigData );
		}
		
		var btn = document.getElementById("btn");
		btn.addEventListener("click",function click(evt){
			console.log("button clicked");
		},/*capturingPhase*/false);
		//为变量显式声明块作用域，并对变量进行本地绑定是非常有用的工具。
```

2.let循环
```
		for(let i=0;i<10;i++){
			console.log(i);
		}
		console.log(i);  //ReferenceError:
		
```
for循环头部的let不仅将i绑定到了for循环的块中，事实上他将其重新绑定到了循环的每一个迭代中，确保使用上一个循环迭代结束时的值重新进行赋值。

3.const
除了let以外，ES6还引入了const，同样可以用来创建块作用域变量，但其值是固定的（常量）。之后任何试图修改值的操作都会引起错误。
```
	var foo = true;
	if(foo){
		var a = 2;
		const b = 3;  //包含在if中的块作用域常量

		a=3;   //正常！
		b=4;  //错误！
	}
	console.log(a); //3
	console.log(b);  //ReferenceError:
```

