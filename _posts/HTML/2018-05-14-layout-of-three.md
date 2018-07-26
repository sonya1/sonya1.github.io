---
layout: post
title: 布局总结2——三栏布局
category: HTML
tags: 三栏布局
keywords: 三栏布局
description: 三栏布局
---

## 三栏布局,左右定宽，中间自适应
![图1](https://sonya1.github.io/assets/img/work_fight/layout_three.png)
### - 1.flex布局--父级display：flex，左右定宽，中间flex：1
```html
<style>
.san1_parent{
  display: flex;
}
.san1_parent .left{
  width: 200px;
  background: pink;
}
.san1_parent .middle{
  flex:1;
  background: #CCFFFF;
}
.san1_parent .right{
  width: 200px;
  background: pink;
}
</style>

<div class="san1_parent">
  <div class="left">left 200</div>
  <div class="middle">middle 自适应</div>
  <div class="right">right 200</div>
</div>
```

### - 2.table布局--父级display：table，左中右display：table-cell，左右定宽，中间不设宽
```html
<style>
.san2_parent{
  display: table;
  width: 100%;
}
.san2_parent .left{
  display: table-cell;
  width: 200px;
  background: pink;
}
.san2_parent .middle{
  display: table-cell;
  background: #CCFFFF;
}
.san2_parent .right{
  display: table-cell;
  width: 200px;
  background: pink;
}
</style>

<div class="san2_parent">
  <div class="left">left 200</div>
  <div class="middle">middle 自适应</div>
  <div class="right">right 200</div>
</div>
```

### - 3.流式布局--左右中，左侧左浮动，右侧右浮动，中间margin-left，margin-right
```html
<style>
.san3_parent .left{
  float: left;
  width: 200px;
  background: pink;
}
.san3_parent .right{
  float: right;
  width: 200px;
  background: pink;
}
.san3_parent .middle{
  margin-left:200px;
  margin-right:200px;
  background: #CCFFFF;
}
</style>

<div class="san3_parent">
  <div class="left">left 200</div>
  <div class="right">right 200</div>
  <div class="middle">middle 自适应</div>
</div>
```

### - 4.BFC布局--左右中，左侧左浮动，右侧右浮动，中间overflow：hidden（BFC）
```html
<style>
.san4_parent .left{
  float: left;
  width: 200px;
  background: pink;
}
.san4_parent .right{
  float: right;
  width: 200px;
  background: pink;
}
.san4_parent .middle{
  overflow: hidden;
  background: #CCFFFF;
}
</style>

<div class="san4_parent">
  <div class="left">left 200</div>
  <div class="right">right 200</div>
  <div class="middle">middle 自适应</div>
</div>
```
### - 5.圣杯布局--中左右，中间100%宽，左右定位
1.将三者都 float:left , 再加上一个position:relative (因为相对定位后面会用到）

2.middle部分 width:100%占满

3.此时middle占满了，所以要把left拉到最左边，使用margin-left:-100%

4.这时left拉回来了，但会覆盖middle内容的左端，要把middle内容拉出来，所以在外围container加上 padding:0 220px 0 200px

5.middle内容拉回来了，但left也跟着过来了，所以要还原，就对left使用相对定位 left:-200px  同理，right也要相对定位还原 right:-220px

6.到这里大概就自适应好了。如果想container高度保持一致可以给left middle right都加上min-height:130px

```html
<style>
.san5_parent{
  padding:0 220px 0 200px;
  overflow:hidden;
}
.san5_parent .middle,.san5_parent .left,.san5_parent .right{
  float:left;
  position: relative;
  min-height: 130px;
}
.san5_parent .middle{
  width:100%;
  background: #CCFFFF;
}
.san5_parent .left{
  margin-left: -100%;
  left:-200px;
  width: 200px;
  background: pink;
}
.san5_parent .right{
  margin-left: -220px;
  right: -220px;
  width: 220px;
  background: pink;
}
</style>

<div class="san5_parent">
  <div class="middle">middle 自适应</div>
  <div class="left">left 200</div>
  <div class="right">right 220</div>
</div>
```
===================2018-06-19 新增flex实现==========================
### - 5.2 圣杯布局 flex实现
```html
<body class="HolyGrail">
  <header>...</header>
  <div class="HolyGrail-body">
    <main class="HolyGrail-content">...</main>
    <nav class="HolyGrail-nav">...</nav>
    <aside class="HolyGrail-ads">...</aside>
  </div>
  <footer>...</footer>
</body>
```
```css
.HolyGrail {
  display: flex;
  min-height: 100vh;
  flex-direction: column;
}

header,
footer {
  flex: 1;
}

.HolyGrail-body {
  display: flex;
  flex: 1;
}

.HolyGrail-content {
  flex: 1;
}

.HolyGrail-nav, .HolyGrail-ads {
  /* 两个边栏的宽度设为12em */
  flex: 0 0 12em;
}

.HolyGrail-nav {
  /* 导航放到最左边 */
  order: -1;
}

/*如果是小屏幕，躯干的三栏自动变为垂直叠加。*/
@media (max-width: 768px) {
  .HolyGrail-body {
    flex-direction: column;
    flex: 1;
  }
  .HolyGrail-nav,
  .HolyGrail-ads,
  .HolyGrail-content {
    flex: auto;
  }
}
```

### - 6.双飞翼布局--中左右，中间padding/margin
1.html代码中，main要放最前边，sub  extra

2.将main  sub  extra 都float:left

3.将main占满 width:100%

4.此时main占满了，所以要把sub拉到最左边，使用margin-left:-100%  同理 extra使用margin-left:-220px

（这时可以直接继续上边圣杯布局的步骤，也可以有所改动）

5.main内容被覆盖了吧，除了使用外围的padding，还可以考虑使用margin。

给main增加一个内层div-- main-inner, 然后margin:0 220px 0 200px

6.main正确展示

```html
<style>
.san6_parent{

}
.san6_parent .middle,.san6_parent .left,.san6_parent .right{
  float:left;
  min-height:130px;
}
.san6_parent .middle{
  width:100%;
  background: #CCFFFF;
}
.san6_parent .middle .middle-inner{
  margin:0 220px 0 200px;
}
.san6_parent .left{
  margin-left: -100%;  /*拉到最左,100% 不能写为px*/  
  width: 200px;
  background: pink;
}
.san6_parent .right{
  margin-left: -220px;  /*拉到最右，不能写成100%*/
  width: 220px;
  background: pink;
}
</style>
    
<div class="san6_parent">
  <div class="middle">
    <div class="middle-inner">middle 自适应</div>
  </div>
  <div class="left">left 200</div>
  <div class="right">right 220</div>
</div>
```
### - 7.绝对定位
```html
<style>
.san7_parent{
  position: relative;
}
.san7_parent .left{
  position: absolute;
  top:0;
  left:0;
  width:200px;
  background: #FFFF99;
}
.san7_parent .middle{
  margin: 0 200px 0 200px;
  background: #993399;
}
.san7_parent .right{
  position: absolute;
  top:0;
  right:0;
  width:200px;
  background: #FFFF99;
}
</style>
    
<div class="san7_parent">
  <div class="left">left 200</div>
  <div class="right">right 200</div>
  <div class="middle">middle 自适应</div>
</div>
```
