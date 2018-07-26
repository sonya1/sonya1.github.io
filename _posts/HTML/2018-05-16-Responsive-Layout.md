---
layout: post
title: 布局方式之响应式布局
category: HTML
tags: 响应式布局
keywords: 响应式布局
description: 布局方式之响应式布局
---

## 一 理解几种布局的概念
### 1、静态布局（Static Layout）

即传统Web设计，对于PC设计一个Layout，在屏幕宽高有调整时，使用横向和竖向的滚动条来查阅被遮掩部分；

意思就是不管浏览器尺寸具体是多少，网页布局就按照当时写代码的布局来布置；

对于移动设备，单独设计一个布局，使用不同的域名如wap.或m.。

### 2、弹性布局
弹性布局是CSS3引入的强大的布局方式，用来替代以前Web开发人员使用的一些复杂而易错hacks方法（如使用float进行类似流式布局）。

其中flex-flow是flex-direction和flex-wrap属性的简写方式，语法如下：

flex-flow: ||

flex-direction: row（初始值） | row-reverse | column | column-reverse

flex-wrap: nowrap（初始值） | wrap | wrap-re‘

### 3、自适应布局（Adaptive Layout）
自适应布局（Adaptive）的特点是分别为不同的屏幕分辨率定义布局。布局切换时页面元素发生改变，但在每个布局中，页面元素不随窗口大小的调整发
生变化。

你可以把自适应布局看作是静态布局的一个系列。

就是说你看到的页面，里面元素的位置会变化而大小不会变化；

### 4、流式布局（Liquid Layout）
流式布局（Liquid）的特点（也叫”Fluid”) 是页面元素的宽度按照屏幕进行适配调整，主要的问题是如果屏幕尺度跨度太大，那么在相对其原始设计而
言过小或过大的屏幕上不能正常显示。

你看到的页面，元素的大小会变化而位置不会变化——这就导致如果屏幕太大或者太小都会导致元素无法正常显示。

### 5、响应式布局（Responsive Layout）
做手机网站必加的一句头部(head)代码
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, 
user-scalable=no">
```

name=”viewport” 名称=视图

width=device-width 页面宽度=设备宽度(可以理解为获取你手机的屏幕宽度)

initial-scale - 初始的缩放比例

minimum-scale - 允许用户缩放到的最小比例

maximum-scale - 允许用户缩放到的最大比例

user-scalable - 用户是否可以手动缩放

分别为不同的屏幕分辨率定义布局，同时，在每个布局中，应用流式布局的理念，即页面元素宽度随着窗口调整而自动适配。

可以把响应式布局看作是流式布局和自适应布局设计理念的融合。

每个屏幕分辨率下面会有一个布局样式，同时位置会变而且大小也会变。

## 二 响应式布局步骤
### 1.布局及设置meta标签
当创建一个响应式网站，或者非响应式网站变成响应式的时候，首先要关注元素的布局。我在创建响应式布局的时候习惯先写非响应式布局，页面固定宽
度大小，我觉得这个对在座的各位没有任何难度。如果完成了非响应式那么我在去添加媒体查询（Media Query)和响应式代码。这种操作更容易实现响
应式特性。

当你完成当你已经完成了无响应的网站，做的第一件事是在你的 HTML 页面，粘贴下面的代码到和标签之间。这将设置屏幕按1：1的尺寸显示，
在 iPhone 和其他智能手机的浏览器提供网站全视图浏览，并禁止用户缩放页面。
```html
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
<meta name="HandheldFriendly" content="true">
```

user-scalable属性能够解决ipad切换横屏之后触摸才能回到具体尺寸的问题。

### 2.通过媒体查询来设置样式media query
media query 是响应式设计的核心，它能够和浏览器进行沟通，告诉浏览器页面如何呈现，假如一个终端的分辨率小于980px，那么可以这样写
```css
@media screen and (max-width:980px){
     #head { … }
     #content { … }
     #footer { … }
```

这里面的样式会覆盖掉之前所定义的样式。

### 3.设置多种视图宽度
假如我们要兼容ipad和iphone视图，我们可以这样设置：
```css
/**ipad**/
@media only screen and (min-width:768px)and(max-width:1024px){}
/**iphone**/
 @media only screen and (width:320px)and (width:768px){}
```

### 4.字体设置
到目前为止，开发人员用到的字体单位大部分都是像素，虽然像素在普通网站上是Ok的，但是我们仍然需要响应式字体。一个响应式的字体应关联它的父
容器的宽度，这样才能适应客户端屏幕。

css3引入了新的单位叫做rem,和em类似但对于Html元素，rem更方便使用。

rem是相对于根元素的，不要忘记重置根元素字体大小：
```css
html{font-size:100%;}
//完成后，你可以定义响应式字体：
@media (min-width:640px){body{font-size:1rem;}}
@media (min-width:960px){body{font-size:1.2rem;}}
@media (min-width:1200px){body{font-size:1.5rem;}}
```

不理解rem的童鞋，在这里给大家推荐一篇写的不错的博客(http://www.cnblogs.com/YYvam1288/p/5123272.html)

#### 如何使用rem单位？
1.确定计数：一般10px，自己记住就行，不用写进代码里

2.
```css
html{font-size:百分数;}  /*百分数：基数/16*/
```

基数=10px 百分数=10/16 = 62.5%

基数=14px 百分数=14/16 = 87.5%

3.px换算rem

rem=想要设的px值/基数

举例：若容器字体想设为14px，则设为14/10 = 14.4rem

rem是以html的font-size
em是以body的font-size

### 5.响应式设计需要注意的问题

1. 宽度不固定，可以使用百分比
```css
#head{width:100%;}
#content{width:50%;}
```

2.图片处理

在html页面中的图片，比如文章里插入的图片我们都可以通过css样式max-width来进行控制图片的最大宽度，如：
```css
#wrap img{
    max-width:100%;
    height:auto;
}
```

如此设置后ID为wrap内的图片会根据wrap的宽度改变已达到等宽扩充，height为auto的设置是为了保证图片原始的高宽比例，以至于图片不会失真。

除了img标签的图片外我们经常会遇到背景图片，比如logo为背景图片：
```css
#log a{
  display:block;
  width:100%;
  height:40px;
  text-indent:55rem;
  background-img:url(logo.png);
  background-repeat:no-repeat;
  background-size:100% 100%;
}
```

background-size是css3的新属性，用于设置背景图片的大小，有两个可选值，第一个值用于指定背景图的width,第2个值用于指定背景图的height,
如果只指定一个值，那么另一个值默认为auto。

background-size:cover; 等比扩展图片来填满元素

background-size:contain; 等比缩小图片来适应元素的尺寸

### 最后我们来总结下响应式布局的实现原理

首先我们应该遵循移动端优先，交互和设计以移动端为主，pc则作为移动端的扩展，一个页面需要兼容不同终端，那么有两个关键点是我们需要去做到
响应式的：响应式布局和响应式内容（图片、多媒体）

- 响应式布局
- Meta标签定义
- 使用Media Queries适配对应样式
- 响应式内容
- 响应式图片

原文来自：[媛媛的小窝——web前端开发之几种布局方式之响应式布局](http://www.zyy1217.com/2016/12/19/web%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91%E4%B9%8B%E5%87%A0%E7%A7%8D%E5%B8%83%E5%B1%80%E6%96%B9%E5%BC%8F%E4%B9%8B%E5%93%8D%E5%BA%94%E5%BC%8F%E5%B8%83%E5%B1%80/)

响应式布局入门[响应式布局入门]:(https://www.cnblogs.com/Wayou/p/responsive_design_step_by_step.html)
