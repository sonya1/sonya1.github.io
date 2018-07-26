---
layout: post
title: html实战+教程
category: HTML
keywords: 前端
---


[原文链接]http://www.w3cschool.cn/   

1.你的图片的边框半径应该是50%，这样它就可以形成一个圆形边框。 border-radius:50%;

2.简而言之，每一张图片都应该有一个alt属性！alt属性，也被称为alt text, 是当图片无法加载时显示的替代文本。alt属性对于盲人或视觉损伤的用户理解一幅图片中所描绘的内容非常重要，搜索引擎也会搜索alt属性。

3.如果你想把一个文本输入字段设置为必填项，在你的input元素中加上required属性就可以了，你可以使用：
<input type="text" required>

注意：required属性在Safari浏览器中不起作用，请用其他浏览器来学习，推荐使用Chrome。

4.每一个单选按钮都应该嵌套在它自己的label(标签)元素中。注意：所有关联的单选按钮应该使用相同的name属性。

5.每一个复选按钮都应该嵌套进label元素中。所有关联的复选按钮应该具有相同的name属性。

6.class="class1 class2" 注意：在 HTML 中这些 class 如何排序是无所谓的。然而，在 style 部分中 class 声明的顺序却非常重要，第二个声明总是比第一个具有优先权。因为 .blue-text 是第二个声明，它覆盖了 .pink-text 属性。

7.我们证明了无论在 style 元素 CSS 的哪个位置进行声明，id 声明都会覆盖 class 声明。

8.我们刚刚证明了行内样式（内联样式）将覆盖style 中定义的所有 CSS。

9.CSS 通过使用Important覆盖所有其他样式.

10.html插件： object   embed
```html
<object width="400" height="50" data="bookmark.swf"></object>
<object width="100%" height="500px" data="snippet.html"></object>
<object data="logo.png"></object>

<embed width="400" height="50" src="/statics/demosource/bookmark.swf">
```

12.超链接设置
```html
text-decoration: 参数
主要用途是改变浏览器显示文字链接时的下划线。 
参数取值范围： 
underline：为文字加下划线。
overline：为文字加上划线。
line-through：为文字加删除线。 
blink：使文字闪烁 。
none：不显示上述任何效果。
```

13.背景图片固定
背景图片固定控制背景图片是否随网页的滚动而滚动。如果不设置背景图片固定属性，浏览器默认背景图片随网页的滚动而滚动。为了避免过于花哨的背景图片在滚动时转移浏览者的注意力，一般都设为固定
```html
background-attachment: 参数。 
参数取值范围：
fixed：网页滚动时，背景图片相对于浏览器的窗口而言，固定不动。
scroll：网页滚动时，背景图片相对于浏览器的窗口而言，一起滚动。
```

14. 
```html
单词间距  word-spacing: 间隔距离。

字母间距  letter-spacing: 字母间距。
```

15.文本对齐
```html
text-align: 参数
参数的取值：
left：左对齐。
right：右对齐。
center：居中对齐。
justify：相对左右对齐。

垂直对齐
vertical-align: 参数
top：顶对齐。
bottom：底对齐。
text-top：相对文本顶对齐。
text-bottom：相对文本底对齐。
baseline：基准线对齐。  字母a的下边线
middle：中心对齐。  字母a的上边线
sub：以下标的形式显示。
super：以上标的形式显示。

文本缩进
text-indent: 缩进距离。
12px相当于一个文字距离。
```

16.空格
```html
white-space: 参数
normal 正常
pre 保留
nowrap 不换行
```

17.显示样式 
```html
display: 参数 
参数取值范围： 
block：块级元素，在对象前后都换行 
inline：在对象前后都不换行 
list-item：在对象前后都换行，增加了项目符号 
none：无显示
```

常用标签：
```html
引文区块<BLOCKQUOTE></BLOCKQUOTE> （通常会内缩）
强调<EM></EM> （通常会以斜体显示）

引文<CITE></CITE> （通常会以斜体显示）
码<CODE></CODE> （显示原始码之用）
样本<SAMP></SAMP>
键盘输入<KBD></KBD>
变数<VAR></VAR>
定义<DFN></DFN> （有些浏览器不提供）
地址 <ADDRESS></ADDRESS>
大字<BIG></BIG>
小字<SMALL></SMALL>

底线<U></U> （尚有些浏览器不提供）
删除线<S></S> （尚有些浏览器不提供）
下标<SUB></SUB>
上标<SUP></SUP>
打字机体<TT></TT> （用单空格字型显示）

闪耀<BLINK></BLINK> （有史以来最被嘲弄的标签）

移动字体(走马灯)：<marquee>写上你想写的字</marquee>
```

媒体查询：
```css
@media 

background-size: contain 自适应
background-size: cover   保持比例 充满屏幕（所以会显示不全）
background-size: 100% 100%  充满屏幕
```
			
