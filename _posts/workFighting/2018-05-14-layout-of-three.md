---
layout: post
title: 布局总结2——三栏布局
category: workFighting
tags: 三栏布局
keywords: 三栏布局
description: 三栏布局
---

## 三栏布局,左右定宽，中间自适应
![图1](https://sonya1.github.io/assets/img/work_fight/layout_three.png)
### - 1.flex布局--父级display：flex，左右定宽，中间flex：1
```
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
```
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
```
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
```
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
```
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
### - 6.双飞翼布局--中左右，中间padding
```
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
  margin-left: -100%;  /*拉到最左*/
  width: 200px;
  background: pink;
}
.san6_parent .right{
  margin-left: -220px;  /*拉到最右*/
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
```
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
