---
layout: post
title: jquery中的extend
category: JS
keywords: 前端
---

参考原文：[$.extend与$.fn.extend的区别](https://www.cnblogs.com/xuxiuyu/p/5989743.html)

## $.extend()
把两个或者更多的对象合并到第一个当中。

$.extend([deep],target,object1,object2 [,object n]);

deep为true表示是否深度合并对象，false不深度合并。

相当于类属性，由类直接调用。
```js

//定义
$.extend({
  speak:function(){
    alert("speak");
  }
});

//调用
$.speak()

```



## $.fn.extend()
把对象挂载到jQuery的prototype属性，来扩展一个新的jQuery实例方法

字面理解嘛，这个拓展的是jQuery.fn的方法。jQuery.fn是啥玩意呢？
```js
jQuery.fn = jQuery.prototype = {
      init:funtion(selector,context){
            //..... 
 
     }
}
```
 所以jQuery.fn就是Jquery原型。
 
 在fn上定义的函数，就是在原型上定义的。所以是Jquery对象 可以调用的。
 
 ```js
 //定义
  $.fn.extend({
     speak:function(){
         alert("speak!");
     }
 });
 
 //调用
 $("div").speak();
 ```

大部分插件都是用jQuery.fn.extend()
