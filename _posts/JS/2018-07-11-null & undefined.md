---
layout: post
title: null & undefined
category: JS
tags: null , undefined
keywords: null , undefined
description: null , undefined
---

## 相同点：
undefined与null 在if语句中都会自动转为false，相等运算符甚至报告两者相等。

## 不同点：
1.null表示一个“无”的对象，转成数值为0；undefined是一个表示“无”的原始值，转数值时为NaN。

null表示“没有对象”，即该处不应该有值。典型用法是：

（1）做为函数的参数，表示该函数的参数不是对象。

（2）作为对象原型链的终点。

undefined表示“缺少值”，就是此处应该有一个值，但是还没有定义。典型用法是：

（1）变量被声明了，但没有赋值时，就等于undefined。

（2）调用函数时，应该提供的参数没有提供，该参数等于undefined。

（3）对象没有赋值的属性，该属性的值为undefined。

（4）函数没有返回值时，默认返回undefined。

检测undefined和null，用typeof就可以。


undeclared：未声明也未赋值的变量。访问会报错。

