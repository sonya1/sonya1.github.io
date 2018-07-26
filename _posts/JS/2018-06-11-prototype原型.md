---
layout: post
title: JS中的原型和原型链,prototype和__proto__
category: JS
tags: prototype,原型,原型链,__proto__
keywords: prototype原型
description: 
---

### 一、一切（引用类型）都是对象
判断一个变量是不是对象非常简单。
值类型的类型判断用typeof，引用类型的类型判断用instanceof。
```js
var fn = function () { };
console.log(fn instanceof Object);  // true
```

一切（引用类型）都是对象，对象是属性的集合。

### 二、函数和对象的关系

函数是一种对象

对象都是通过函数创建的
```js
function Fn() {
    this.name = '王福朋';
    this.year = 1988;
}
var fn1 = new Fn();
```

```js
var obj = { a: 10, b: 20 };
var arr = [5, 'x', true];
```
其实是语法糖，
```js
//var obj = { a: 10, b: 20 };
//var arr = [5, 'x', true];

var obj = new Object();
obj.a = 10;
obj.b = 20;

var arr = new Array();
arr[0] = 5;
arr[1] = 'x';
arr[2] = true;
```
所以，可以很负责任的说——对象都是通过函数来创建的。

所以说对象是函数创建的，而函数却又是一种对象。

### 三、prototype原型
***每个函数都有一个属性叫做prototype。***

这个prototype的属性值是一个对象（属性的集合，再次强调！），默认的只有一个叫做constructor的属性，指向这个函数本身。

![原型图1](https://images0.cnblogs.com/blog/138012/201409/172121182841896.png)

如上图，SuperType是是一个函数，右侧的方框就是它的原型。

![原型图2](https://images0.cnblogs.com/blog/138012/201409/172130097842386.png)


```js
 function Fn() { }
  Fn.prototype.name = '王福朋';
  Fn.prototype.getYear = function () {
      return 1988;
  };

  var fn = new Fn();
  console.log(fn.name);
  console.log(fn.getYear());
```

即，Fn是一个函数，fn对象是从Fn函数new出来的，这样fn对象就可以调用Fn.prototype中的属性。

因为每个对象都有一个隐藏的属性——“__proto__”，这个属性引用了创建这个对象的函数的prototype。即：fn.__proto__ === Fn.prototype

这里的"__proto__"成为“隐式原型”.

### 四、__proto__隐式原型

***每个函数function都有一个prototype，即原型。每个对象都有一个__proto__，可成为隐式原型。***

***隐式原型__proto__，指向了此对象构造函数的原型，即构造函数的prototype。***

每个对象都有一个__proto__属性，指向创建该对象的函数的prototype。

~ 问：自定义函数的prototype指向哪里呢？

答：自定义函数的prototype本质上就是和 var obj = {} 是一样的，都是被Object创建，所以它的__proto__指向的就是Object.prototype。

- 问：“Object prototype”也是一个对象，它的__proto__指向哪里？

答：Object.prototype确实一个特例——它的__proto__指向的是null，切记切记！

因为函数也是对象，所以有构造函数的隐式原型为Function.prototype。Object.__proto__ === Function.prototype成立~~

![终极图](https://images0.cnblogs.com/blog/138012/201409/181512068463597.png)

上图中，很明显的标出了：自定义函数Foo.__proto__指向Function.prototype，
Object.__proto__指向Function.prototype，
唉，怎么还有一个……Function.__proto__指向Function.prototype？这不成了循环引用了？

对！是一个环形结构。

其实稍微想一下就明白了。Function也是一个函数，函数是一种对象，也有__proto__属性。既然是函数，那么它一定是被Function创建。
所以——Function是被自身创建的。所以它的__proto__指向了自身的Prototype。

所以总结一下，函数的__proto__最终都指向Function.prototype，而对象的__proto__最终都指向Object.prototype。

### 五、instanceof

规则：对象 instanceof 函数。 

```js
function Foo(){}

var f1 = new Foo();

console.log(f1 instanceof Foo);    //true
console.log(f1 instanceof Object);    //true

```
![instanceof图](https://images0.cnblogs.com/blog/138012/201409/181635468939277.png)

Instanceof运算符的第一个变量是一个对象，暂时称为A；第二个变量一般是一个函数，暂时称为B。

Instanceof的判断队则是：沿着A的__proto__这条线来找，同时沿着B的prototype这条线来找，如果两条线能找到同一个引用，即同一个对象，
那么就返回true。如果找到终点还未重合，则返回false。

```js
console.log(Object instanceof Function)  //true
console.log(Function instanceof Object)  //true
console.log(Function instanceof Function)  //true

```
解释一下这个吧~画个图就明白了。

![instanceof图](https://images0.cnblogs.com/blog/138012/201409/181637013624694.png)

这是个全的图，我自己画的有点惨。。。

instanceof这样设计，引出了原型链——继承。

instanceof表示的就是一种继承关系，或者原型链的结构。

### 六、继承 
javascript中继承是通过原型链来体现的。

***访问一个对象的属性时，先在基本属性中查找，如果没有，再沿着__proto__这条链向上找，这就是原型链。***

如何区分一个属性到底是基本的还是从原型中找到的呢？——hasOwnProperty，特别是在for…in…循环中，一定要注意。

![hasOwnproperty](https://images0.cnblogs.com/blog/138012/201409/182014022217881.png)

BUT f1的这个hasOwnProperty方法是从哪里来的？ f1本身没有，Foo.prototype中也没有，哪儿来的？

它是从Object.prototype中来的，请看图：

![继承2](https://images0.cnblogs.com/blog/138012/201409/182014277067963.png)

### 七、原型链的灵活性
可以添加或修改原型上的属性或方法，所以说很灵活。不像java一样。

原文参考：[王福朋的博客园——深入理解javascript原型](http://www.cnblogs.com/wangfupeng1988/p/3977924.html)
