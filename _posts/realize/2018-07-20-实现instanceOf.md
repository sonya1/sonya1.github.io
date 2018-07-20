---
layout: post
title: 实现instanceOf
category: realize
tags: 实现instanceOf
keywords: 实现instanceOf
description: 实现instanceOf
---

 instanceof 主要的实现原理就是只要右边变量的 prototype 在左边变量的原型链上即可。
 
 因此，instanceof 在查找的过程中会遍历左边变量的原型链，直到找到右边变量的 prototype，如果查找失败，则会返回 false，告诉我们左边变量并非是右边变量的实例。
 
```js
function instance(left, right) {
    // 获得类型的原型
    let prototype = right.prototype;
    // 获得对象的原型
    left = left.__proto__;
    // 判断对象的类型是否等于类型的原型
    while (true) {
    	if (left === null)
    		return false;
    	if (prototype === left)
    		return true;
    	left = left.__proto__;
    }
}
```
