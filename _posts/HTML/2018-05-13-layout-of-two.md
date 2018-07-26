---
layout: post
title: 布局总结1——两栏布局
category: HTML
tags: 两栏布局
keywords: 两栏布局
description: 两栏布局
---

## 两栏布局，左侧定宽，右侧自适应
![图1](https://sonya1.github.io/assets/img/work_fight/layout_two.png)
### - 1.浮动布局：左侧定宽:float：left，右侧：margin-left：左侧宽度
```html
<style>
.er1_parent .left{
    float:left;
    width:200px;
    background: #99CC99;
  }
  .er1_parent .right{
    margin-left: 200px;
    background: #FFCC99;
  }
</style>

<div class="er1_parent">
		<div class="left">left 200</div>
		<div class="right">right 自适应</div>
</div>
```

### - 2.BFC布局：左侧定宽、float：left，右侧：overflow：hidden（BFC）
```html
<style>
.er2_parent .left{
  float:left;
  width:200px;
  background: #99CC99;
}
.er2_parent .right{
  overflow: hidden;
  background: #FFCC99;
}
</style>

<div class="er2_parent">
  <div class="left">left 200</div>
  <div class="right">right 自适应</div>
</div>
```

### - 3.flex布局：父级dislpay：flex，左侧定宽，右侧：flex：1
```html
<style>
.er3_parent{
  display: flex;
}
.er3_parent .left{
  flex:0 0 200px;
  width:200px;
  background: #99CC99;
}
.er3_parent .right{
  flex:1;
  background: #FFCC99;
}
</style>

<div class="er3_parent">
  <div class="left">left 200</div>
  <div class="right">right 自适应</div>
</div>
```

### - 4.绝对定位布局：父级相对定位，左侧绝对定位，定在左侧，右侧margin-left
```html
<style>
.er4_parent{
  position: relative;
}
.er4_parent .left{
  position: absolute;
  top:0;
  left:0;
  width:200px;
  background: #99CC99;
}
.er4_parent .right{
  margin-left: 200px;
  background: #FFCC99;
}
</style>

<div class="er4_parent">
  <div class="left">left 200</div>
  <div class="right">right 自适应</div>
</div>
```
