---
layout: post
title: hover操作以及兄弟选择器+，~
category: HTML
tags: hover,兄弟选择器
keywords: hover,兄弟选择器
description: hover,兄弟选择器
---

### 1.a与b是相邻兄弟关系，有相同的父节点
可以通过 #a + #b{..}来控制#b里面的元素样式；
```html
<style>
  #a {color : red;}
  #a:hover + #b{color : blue;}
</style>

<div id='a'>元素1</div>
<div id='b'>元素2</div>
<div id='c'>元素3</div>
```
元素1显示红色，hover元素1时，元素2显示蓝色。css选择器 + 表示紧接在一个元素后的元素，而且二者有相同的父元素，即相邻兄弟选择器。 

### 2. a 和b 是普通的兄弟关系, 可以通过#a:hover ~ #b来控制b元素里面的元素样式
```html
ul li:nth-child(1):hover~.move{
    background:pink;
}

<ul>
    <li>首页</li>
    <li>最新活动</li>
    <li>项目介绍</li>
    <li>爱心社区</li>
    <li>关于我们</li>
    <li class="move"></li>
</ul>
```
鼠标移动到“首页”，最后一个li背景变粉色。

### 3.hover操作自己的子集元素以及所属子集元素的元素
通过 > 与 + 来控制下个元素，子集元素，下个元素的子集元素的CSS

```html
<style>
    #a {color : red;}
    #a:hover > .b{color : yellow;} 
    #a:hover + #c{color : blue;}
    #a:hover + #c > .b{color : green;}
</style>

<div id='a'>元素a
  <div class='b'>元素b</div>
</div>
<div id='c'>元素c
  <div class='b'>元素b2</div>
</div>
```

初始元素a，元素b显示 红色，当鼠标移到元素a（元素b）上时，元素b变 黄色，元素c变蓝色，b2变绿色。


### 1.相邻兄弟选择器 +
```html
<style type="text/css">
    h1 + p {
        margin-top:50px;
        color:red;
    }
</style>

<body>
<p>This is paragraph.</p>
<h1>This is a heading.</h1>
<p>This is paragraph.</p>
<p>This is paragraph.</p>
```
效果图
![图1](https://sonya1.github.io/assets/img/work_fight/sibling_selection1.png)

兄弟只会影响下面的p标签的样式，不影响上面兄弟的样式。 

注意这里的’+’的意义跟’and’意义不一样，兄弟选择器的样式是应用在兄弟元素上，跟参照的元素样式无关，如上例只影响p元素的样式，而不影响
h1标签的样式。 

当然这个也会循环查找，即当两个兄弟元素相同时，会一次循环查找： 

```html
<style type="text/css">
    li + li {
        color:red;
    }
</style>

<div>
  <ul>
    <li>List item 1</li>
    <li>List item 2</li>
    <li>List item 3</li>
  </ul>
</div>
```
![图2](https://sonya1.github.io/assets/img/work_fight/sibling_selection2.png)

可以看出第一个li字体颜色没有变红，第二个和第三个元素字体变红，这就是因为第三个li是第二个li的兄弟元素，所以也会应用样式。

### 2.普通兄弟选择器~
作用是查找某一个指定元素的后面的所有兄弟结点。（注意：后面的所有兄弟） 
```html
<style type="text/css">
    h1 ~ p{
        color:red;
    }
</style>

<body>
    <p>1</p>
    <h1>2</h1>
    <p>3</p>
    <p>4</p>
    <p>5</p>
</body>
```
![图3](https://sonya1.github.io/assets/img/work_fight/sibling_selection3.png)
