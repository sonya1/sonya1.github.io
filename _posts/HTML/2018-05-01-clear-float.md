---
layout: post
title: 清除浮动的方法
category: HTML
tags: 清除浮动
keywords: 清除浮动
description: 清除浮动
---

## float浮动
float属性定义元素在哪个方向浮动。在CSS中，任何元素都可以浮动。浮动元素会生成一个块级框，而不论它本身是何种元素。同时默认情况下，盒子的宽度不在伸展，而是根据盒子里面的内容的宽度来确定。

## 浮动带来的弊端
1.块状元素，会跑到浮动下面，被覆盖了

2.行内文字会围绕浮动环绕，为浮动元素留出空间

3.浮动元素的父元素塌陷


##  清除浮动--解决高度塌陷
### 1. 给浮动元素的父元素内末尾添加带clear属性的空元素
```html
<div style="clear:both;"></div>
```
新的空div块下移，达到撑开父元素的目的。

- 优点：
简单，代码少，浏览器兼容性好。

- 缺点：
需要添加无意义的html元素，代码不够优雅，后期不易维护。


### 2.给浮动元素的容器添加浮动
```css
.container{
  float:left;
}
```
- 可清除内部浮动，但是会使整体浮动，影响布局，不推荐。

### 3. 给浮动元素的容器（父元素）添加overflow + zoom
```css
.fix{
  overflow:hidden; 
  zoom:1;
}
```

IE下清除浮动很简单，使元素haslayout=true就可以了。不管是设置宽度值、高度值（除了auto）、display（=inline-block）、绝对定位、zoom
（不为normal或null）、浮动（不为none）本身都可以让元素haslayout为true。

zoom是IE专有属性，可以设置对象缩放比例。显然，首选zoom:1，这样做不会干扰任何样式。

非IE浏览器常用的是overflow属性，overflow:hidden;或是overflow:scroll auto都可以，不过由于后者经常一不小心出现滚动条，
所以前者用的更多些。

- 此方法优点在于代码简洁，涵盖所有浏览器，可谓不错的选择啊。不过也是有问题的，就是这个overflow:hidden;是个小炸蛋，要是里面的元素要是
想来个margin负值定位或是负的绝对定位，岂不是直接被裁掉了，所以此方法是有不小的局限性的。

### 4. 给浮动元素的容器添加after伪类 + zoom
先来简单讲讲after，所谓after，就是指标签的最后一个子元素的后面。于是呢，我们可以用CSS代码生成一个具有clear属性的元素，其中的关键样式
就是content了。或许您从网上看到的content里面的内容是”.”一个点，我试了很多次，貌似随便写什么东西都没有问题，
比如content:’anything is ok’;没问题。于是有：
```css
.fix{
  zoom : 1;
}
.fix : after{
  display : block; 
  content : ' '; 
  clear : both; 	
  line-height : 0; //height:0
}
```
这里的 line-height:0 写成height:0也是可以的。此方法可以说是综合起来最好的方法了，不会影响任何其他样式，通用性强，覆盖面广，比较推荐。

### float与JavaScript
JavaScript可以改变CSS的属性，其他些属性还好，但是这个float值得一说，为何呢，因为float貌似是JavaScript中的一个关键字，不能使用
obj.style.float="left";这样的句子，得使用其他写法。
```css
//IE浏览器：
obj.style.styleFloat = "left";

//其他浏览器：
obj.style.cssFloat = "left";
```
