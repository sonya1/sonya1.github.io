---
layout: post
title: 盒子、文字水平、垂直居中 大全
category: HTML
tags: 水平、垂直居中
keywords: 水平、垂直居中
description: 水平、垂直居中
---

## 文字
### - 1.单行文本垂直居中：
```css
height = line-height
```

### - 2.n行文本垂直居中：
不用设高：上下padding相同    

设高：
```css
//父元素设高，
height:300px;
display：table;

//子元素
display:table-cell;
vertical-align:middle;
```

### - 3.文字水平居中:text-align:center

## 盒子
### - 1.盒子仅设宽度
width:200px;margin:0 auto
父元素设上下相同的padding

### - 2.盒子仅设高度（flex）
父元素设高：
```css
height：300px;
display:flex;
justify-content:center; //水平居中
align-items:center; //垂直居中
```
### - 3.盒子设宽、高(绝对定位)
```css
height:300px;
width:300px;
position:absolute;
top:50%;
left:50%;
margin-left:-(宽度+padding)/2;
margin-top:-(高度+padding)/2;
//transform:translate(-50%,-50%);
```

### - 4.盒子设宽、高(水平居中)
```css
div{
    margin-left: auto;
    margin-right: auto;
    height:300px;
    width:300px;
}
```
