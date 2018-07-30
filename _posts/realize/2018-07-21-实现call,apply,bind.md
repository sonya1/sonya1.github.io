---
layout: post
title: 实现call,apply,bind
category: realize
tags: 实现call,apply,bind
keywords: 实现call,apply,bind
description: 实现call,apply,bind
---

## 可以从以下几点来考虑如何实现

1.不传入第一个参数，那么默认为 window

2.改变了 this 指向，让新的对象可以执行该函数。那么思路是否可以变成给新的对象添加一个函数，然后在执行完以后删除？

```js

//模拟实现call()
Function.prototype.myCall = function (context) {
  var context = context || window
  // 给 context 添加一个属性
  // getValue.call(a, 'yck', '24') => a.fn = getValue
  context.fn = this
  // 将 context 后面的参数取出来
  var args = [...arguments].slice(1)
  // getValue.call(a, 'yck', '24') => a.fn('yck', '24')
  var result = context.fn(...args)
  // 删除 fn
  delete context.fn
  return result
}


//模拟实现apply()
Function.prototype.myApply = function (context) {
  var context = context || window
  context.fn = this

  var result
  // 需要判断是否存储第二个参数
  // 如果存在，就将第二个参数展开
  if (arguments[1]) {
    result = context.fn(...arguments[1])
  } else {
    result = context.fn()
  }

  delete context.fn
  return result
}
```


bind 和其他两个方法作用也是一致的，只是该方法会返回一个函数。并且我们可以通过 bind 实现柯里化。

```js
function bind(fn, context){
  return function(){
    return fn.apply(context, arguments);
  };
}


Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  var _this = this
  var args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    if (this instanceof F) {
      return new _this(args, ...arguments)
    }
    return _this.apply(context, args.concat(arguments))
  }
}
```

bind实现详见:[https://segmentfault.com/a/1190000009271416](https://segmentfault.com/a/1190000009271416)
