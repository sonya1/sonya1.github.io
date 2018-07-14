---
layout: post
title: Javascript深克隆原理与实现
category: JS
tags: Javascript深克隆
keywords: Javascript深克隆
description: Javascript深克隆原理与实现
---

原文来自：[https://www.cnblogs.com/Chen-XiaoJun/p/6217373.html](https://www.cnblogs.com/Chen-XiaoJun/p/6217373.html)

## 深拷贝的实现方式
要完全复制又不能修改到原对象，这时候就要用 Deep Copy，这里会介绍几种Deep Copy 的方式。

### 1、手动复制

把一个对象的属性复制给另一个对象的属性
```js
var obj1 = { a: 10, b: 20, c: 30 };
var obj2 = { a: obj1.a, b: obj1.b, c: obj1.c };
obj2.b = 100;
console.log(obj1);
// { a: 10, b: 20, c: 30 } <-- 沒被改到
console.log(obj2);
// { a: 10, b: 100, c: 30 }
```
但这样很麻烦，要一个一个自己复制；而且这样的本质也不能算是 Deep Copy，因为对象里面也可能回事对象，如像下面这个状况：

```js
var obj1 = { body: { a: 10 } };
var obj2 = { body: obj1.body };
obj2.body.a = 20;
console.log(obj1);
// { body: { a: 20 } } <-- 被改到了
console.log(obj2);
// { body: { a: 20 } }
console.log(obj1 === obj2);
// false
console.log(obj1.body === obj2.body);
// true
```
虽然obj1跟obj2是不同对象，但他们会共享同一个obj1.body，所以修改obj2.body.a时也会修改到旧的。

### 2、对象只有一层的话可以使用：Object.assign()函数

Object.assign({}, obj1)的意思是先建立一个空对象{}，接着把obj1中所有的属性复制过去，所以obj2会长得跟obj1一样，这时候再修改obj2.b也不会影响obj1。

因为Object.assign跟我们手动复制的效果相同，所以一样只能处理深度只有一层的对象，没办法做到真正的 Deep Copy。不过如果要复制的对象只有一层的话可以考虑使用它。

### 3、转成 JSON 再转回来

用JSON.stringify把对象转成字符串，再用JSON.parse把字符串转成新的对象。
```js
var obj1 = { body: { a: 10 } };
var obj2 = JSON.parse(JSON.stringify(obj1));
obj2.body.a = 20;
console.log(obj1);
// { body: { a: 10 } } <-- 沒被改到
console.log(obj2);
// { body: { a: 20 } }
console.log(obj1 === obj2);
// false
console.log(obj1.body === obj2.body);
// false
```
这样做是真正的Deep Copy，这种方法简单易用。

但是这种方法也有不少坏处，譬如它会抛弃对象的constructor。也就是深拷贝之后，不管这个对象原来的构造函数是什么，在深拷贝之后都会变成Object。

这种方法能正确处理的对象只有 Number, String, Boolean, Array, 扁平对象，即那些能够被 json 直接表示的数据结构。RegExp对象是无法通过这种方式深拷贝。

也就是说，只有可以转成JSON格式的对象才可以这样用，像function没办法转成JSON。
```js
var obj1 = { fun: function(){ console.log(123) } };
var obj2 = JSON.parse(JSON.stringify(obj1));
console.log(typeof obj1.fun);
// 'function'
console.log(typeof obj2.fun);
// 'undefined' <-- 没复制
```
要复制的function会直接消失，所以这个方法只能用在单纯只有数据的对象。

### 4、递归拷贝
```js
function deepClone(initalObj, finalObj) {    
  var obj = finalObj || {};    
  for (var i in initalObj) {        
    if (typeof initalObj[i] === 'object') {  //数组或对象
      obj[i] = (initalObj[i].constructor === Array) ? [] : {};            
      arguments.callee(initalObj[i], obj[i]);  //递归调用
    } else {
      obj[i] = initalObj[i];
    }
  }    
  return obj;
}

var str = {};
var obj = { a: {a: "hello", b: 21} };
deepClone(obj, str);
console.log(str.a);
```
上述代码确实可以实现深拷贝。但是当遇到两个互相引用的对象，会出现死循环的情况。

为了避免相互引用的对象导致死循环的情况，则应该在遍历的时候判断是否相互引用对象，如果是则退出循环。

改进版代码如下：????
```js
function deepClone(initalObj, finalObj) {    
  var obj = finalObj || {};    
  for (var i in initalObj) {        
    var prop = initalObj[i];        // 避免相互引用对象导致死循环，如initalObj.a = initalObj的情况
    if(prop === obj) {            
      continue;
    }        
    if (typeof prop === 'object') {
      obj[i] = (prop.constructor === Array) ? [] : {};            
      arguments.callee(prop, obj[i]);
    } else {
      obj[i] = prop;
    }
  }    
  return obj;
}


var str = {};
var obj = {};
obj.a = obj;
obj.b = 21;
deepClone(obj, str);
console.log(str.a);
```
### 5、使用Object.create()方法

直接使用var newObj = Object.create(oldObj)，可以达到深拷贝的效果。
```js
function deepClone(initalObj, finalObj) {    
  var obj = finalObj || {};    
  for (var i in initalObj) {        
    var prop = initalObj[i];        // 避免相互引用对象导致死循环，如initalObj.a = initalObj的情况
    if(prop === obj) {            
      continue;
    }        
    if (typeof prop === 'object') {
      obj[i] = (prop.constructor === Array) ? [] : Object.create(prop);
    } else {
      obj[i] = prop;
    }
  }    
  return obj;
}
```
### 6、jquery

jquery 有提供一个$.extend可以用来做 Deep Copy。
```js
var $ = require('jquery');
var obj1 = {
    a: 1,
    b: { f: { g: 1 } },
    c: [1, 2, 3]
};
var obj2 = $.extend(true, {}, obj1);
console.log(obj1.b.f === obj2.b.f);
// false
```
### 7、lodash

另外一个很热门的函数库lodash，也有提供_.cloneDeep用来做 Deep Copy。
```js
var _ = require('lodash');
var obj1 = {
    a: 1,
    b: { f: { g: 1 } },
    c: [1, 2, 3]
};
var obj2 = _.cloneDeep(obj1);
console.log(obj1.b.f === obj2.b.f);
// false
```
这个性能还不错，使用起来也很简单。

## 8.数组的slice，concat只能深拷贝一层 要注意
```js
var a=[1,2,3,4],
    b=a.slice();
a[0]=2;
console.log(a,b);   //[2,2,3,4]  [1,2,3,4]  实现深拷贝了

var a=[0,1,[2,3],4],
        b=a.slice();
a[0]=1;
a[2][0]=1;
console.log(a,b);  // [1,1,[1,3],4]  [0,1,[1,3],4] 第二层的还是共用了
```
所以，数组的slice，concat只能深拷贝一层，与Object.assign()函数类似，不是真正的深拷贝。

## ES6数组的深拷贝 
第一种：Array.from(要复制的数组);
```js
var arr1=[1,2,3];
var arr2=Array.from(arr1);
arr1.push(4);
alert(arr1);  //1234
alert(arr2);  //123
arr2.push(5);
alert(arr1);  //1234
alert(arr2);  //1235
```

第二种：...
```js
var arr1=[1,2,3];
var arr2=[...arr1];
arr1.push(4);
alert(arr1);  //1234
alert(arr2);  //123
arr2.push(5);
alert(arr1);  //1234
alert(arr2);  //1235
```
第二种这个方法也可以用在函数的形参上面。
```js
function show(...arr1){  //直接来复制arguments这个伪数组，让它变成真正的数组，从而拥有数组的方法。
  alert(arr1); //1234
  arr1.push(5);
  alert(arr1); //12345
}
show(1,2,3,4)

```

深拷贝的问题：
```js
function P(obj){}

P.prototype.caa=function(){console.log(1)};

var p =new RegExp('/[.]/');

var obj1;

obj1=new P();

var newObj;

obj1={a:1,b:2,c:3,d:{a:1,b:2,c:3,d:{a:1,b:2,c:3,d:{a:1,b:2,c:3,d:Date.now(),e:p}}}};//一个多层嵌套对象

function objCopy(obj){
    var copy_obj={};
    for(var i in obj){
        if(typeof obj[i]=='object'){//如果对应KEY的数据类型是object的话，就进行递归调用
              copy_obj[i]=objCopy(obj[i]);
        }else{
              copy_obj[i]=obj[i];
        }
    }
    return copy_obj;
}

var newObj=objCopy(obj1);

console.log(obj1);

console.log(newObj);
//正则表达式 赋值有问题
```

## 循环引用的问题解决

原文查看：[http://www.24-80.com/javascript/article-97.html](http://www.24-80.com/javascript/article-97.html)
