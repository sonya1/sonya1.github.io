---
layout: post
title: CSS中的绝对定位与相对定位
category: CSS
keywords: 前端
---



层级关系为：

<div ——————————— position:relative; 不是最近的祖先定位元素，不是参照物

<div—————————-没有设置为定位元素，不是参照物

<div———————- position:relative 参照物

<div box1

<div box2 ——–position:absolute; top:50px; left:120px;

<div box3

效果图：

![图](https://sonya1.github.io/assets/img/blog/position_1.jpg)


为改变参照物（橘色框）后的效果
层级关系为：
<div ——————————— position:relative;最近的祖先定位元素，参照物

<div—————————-没有设置为定位元素，不是参照物

<div———————-没有设置为定位元素，不是参照物

<div box1

<div box2 ——–position:absolute; top:50px; left:120px;

<div box3
效果图：
![图](https://sonya1.github.io/assets/img/blog/position_2.jpg)

参照物为最顶级的元素情况。

层级关系为：

<div ———————————没有设置为定位元素，不是参照物

<div—————————-没有设置为定位元素，不是参照物

<div———————-没有设置为定位元素，不是参照物

<div box1

<div box2 ——–position:absolute; top:50px; left:120px;

<div box3

效果图：
![图](https://sonya1.github.io/assets/img/blog/position_3.jpg)


仅使用margin属性布局绝对定位元素的情况

此情况，margin-bottom 和margin-right的值不再对文档流中的元素产生影响，因为该元素已经脱离了文档流。另外，不管它的祖先元素有没有定位，都是以文档流中
原来所在的位置上偏移参照物。  

图9中，使用margin属性布局相对定位元素。

层级关系为：

<div ——————————— position:relative; 不是参照物

<div—————————-没有设置为定位元素，不是参照物

<div———————-没有设置为定位元素，不是参照物

<div box1

<div box2 ——–position:absolute; margin-top:50px; margin-left:120px;

<div box3
效果图：

![图](https://sonya1.github.io/assets/img/blog/position_4.jpg)


IE6的情况下，box2前面没有兄弟节点，则margin-left的值会出现双倍边距，见图10。

层级关系为：

<div ——————————— position:relative; 不是参照物

<div—————————-没有设置为定位元素，不是参照物

<div———————-没有设置为定位元素，不是参照物

<div box1

<div box2 ——–position:absolute; margin-top:50px; margin-left:60px;

<div box3

效果图：
![图](https://sonya1.github.io/assets/img/blog/position_5.jpg)

转自：http://www.cnblogs.com/jiqing9006/archive/2012/07/26/2610586.html
