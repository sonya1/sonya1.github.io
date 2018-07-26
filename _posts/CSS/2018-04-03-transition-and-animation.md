---
layout: post
title: CSS3 transition过渡和animation动画
category: CSS
tags: CSS3
keywords: transition/animation
description: CSS3 transition过渡和animation动画
---

原文来自：[媛媛的小窝——CSS3 transition过渡和animation动画](http://www.zyy1217.com/2017/02/21/CSS3%20transition%E8%BF%87%E6%B8%A1%E5%92%8Canimation%E5%8A%A8%E7%94%BB/)

## 一、transition
CSS3的过渡功能就像是一种黄油，可以让CSS的一些变化变得平滑。因为原生的CSS过渡在客户端需要处理的资源要比用JavaScript和Flash少的多，所以才会更平滑。

transition的属性可以分开写，也可以放在一起写,比如下面的代码，图片的宽高本来都是15px，想要让它1秒的时间内过渡到宽高为450px，通过：hover来触发，那么代码就可以如下：
```css
img{
    height:15px;
    width:15px;
    transition: 1s 1s height ease;/*合在一起*/
}
// 或者：
img{
    height：15px;
    width: 15px;
    transition-property: height;
    transition-duration: 1s;
    transition-delay: 1s;
    transition-timing-function: ease;/*属性分开写*/
}
img:hover{
    height: 450px;
    width: 450px;
}
```
因为过渡所需要时间与过渡延迟时间的单位都是秒，所以在合起来写transition的属性的时候，第一个time会解析为transiton-duration，第二个解析为transition-delay。所以，可以给transition一个速记法

transiton: 过渡属性 过渡所需要时间 过渡动画函数 过渡延迟时间；

### 属性详解
- transition-property
不是所有属性都能过渡，只有属性具有一个中间点值才具备过渡效果。

- transition-duration
指定从一个属性到另一个属性过渡所要花费的时间。默认值为0，为0时，表示变化是瞬时的，看不到过渡效果。

- transiton-timing-function
过渡函数，有如下几种：

linear 匀速。规定以相同速度开始至结束的过渡效果（等于 cubic-bezier(0,0,1,1)）。 

ease	规定慢速开始，然后变快，然后慢速结束的过渡效果（cubic-bezier(0.25,0.1,0.25,1)）。

ease-in	规定以慢速开始的过渡效果（等于 cubic-bezier(0.42,0,1,1)）。

ease-out	规定以慢速结束的过渡效果（等于 cubic-bezier(0,0,0.58,1)）。

ease-in-out	规定以慢速开始和结束的过渡效果（等于 cubic-bezier(0.42,0,0.58,1)）。

cubic-bezier(n,n,n,n)	在 cubic-bezier 函数中定义自己的值。可能的值是 0 至 1 之间的数值。

### 触发过渡
单纯的代码不会触发任何过渡操作，需要通过用户的行为（如点击，悬浮等）触发，可触发的方式有：

:hover

:focus

:checked

媒体查询触发

JavaScript触发

### 局限性
transition的优点在于简单易用，但是它有几个很大的局限。

transition需要事件触发，所以没法在网页加载时自动发生。

transition是一次性的，不能重复发生，除非一再触发。

transition只能定义开始状态和结束状态，不能定义中间状态，也就是说只有两个状态。

一条transition规则，只能定义一个属性的变化，不能涉及多个属性。 

CSS Animation就是为了解决这些问题而提出的。

## 二、animation
CSS3的animation属性可以像Flash制作动画一样，通过控制关键帧来控制动画的每一步，实现更为复杂的动画效果。ainimation实现动画效果主要由两部分组成：

通过类似Flash动画中的帧来声明一个动画；

在animation属性中调用关键帧声明的动画。

Note：animation属性到目前位置得到了大多数浏览器的支持，但是，需要添加浏览器前缀哦！需要添加浏览器前缀哦！需要添加浏览器前缀哦！

### animation动画属性

animation-name：none为默认值，将没有任何动画效果，其可以用来覆盖任何动画

animation-duration：默认值为0，意味着动画周期为0，也就是没有任何动画效果

animation-timing-function：与transition-timing-function一样

animation-delay：在开始执行动画时需要等待的时间

animation-iteration-count：定义动画的播放次数，默认为1，如果为infinite，则无限次循环播放

animation-direction：默认为normal，每次循环都是向前播放，（0-100），另一个值为alternate，动画播放为偶数次则向前播放，如果为奇数次就反方向播放

animation-state：默认为running，播放，paused，暂停

animation-fill-mode：定义动画开始之前和结束之后发生的操作，默认值为none，动画结束时回到动画没开始时的状态；
forwards，动画结束后继续应用最后关键帧的位置，即保存在结束状态；backwards，让动画回到第一帧的状态；both：轮流应用forwards和backwards规则。

@keyframes

CSS3的animation制作动画效果主要包括两部分：

用关键帧声明一个动画，
在animation调用关键帧声明的的动画。
@keyframes就是关键帧。这个帧与Flash里的帧类似，一个动画中可以有很多个帧。

一个@keyframes中的样式规则是由多个百分比构成的，可以在这个规则上创建多个百分比，从而达到一种不断变化的效果。另外，@keyframes必须要加webkit前缀。举个例子：
```css
div:hover {
  -webkit-animation: 1s changeColor;
  animation: 1s changeColor;  
}
@-webkit-keyframes changeColor {
  0% { background: #c00; }
  50% { background: orange; }
  100% { background: yellowgreen; }
}
@keyframes changeColor {
  0% { background: #c00; }
  50% { background: orange; }
  100% { background: yellowgreen; }
}
```
上面代码中的0% 100%的百分号都不能省略，0%可以由from代替，100%可以由to代替。要让changeColor动画有效果，就必须要通过CSS3animation属性来调用它。

## 区别
animation属性类似于transition，他们都是随着时间改变元素的属性值，其主要区别在于：

transition需要触发一个事件才会随着时间改变其CSS属性；

animation在不需要触发任何事件的情况下，也可以显式的随时间变化来改变元素CSS属性，达到一种动画的效果
