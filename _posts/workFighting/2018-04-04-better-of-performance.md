---
layout: post
title: 前端性能优化
category: workFighting
tags: 性能优化
keywords: 前端性能优化
description: 前端性能优化
---

原文来自：[媛媛的小窝——前端性能优化](http://www.zyy1217.com/2017/03/01/%E5%89%8D%E7%AB%AF%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96/)

前端是庞大的，包括 HTML、 CSS、 Javascript、Image 、Flash等等各种各样的资源。前端优化是复杂的，针对方方面面的资源都有不同的方式。那么，前端优化的目的是什么 ?

　　1. 从用户角度而言，优化能够让页面加载得更快、对用户的操作响应得更及时，能够给用户提供更为友好的体验。
　　2. 从服务商角度而言，优化能够减少页面请求数、或者减小请求所占带宽，能够节省可观的资源。

总之，恰当的优化不仅能够改善站点的用户体验并且能够节省相当的资源利用。
　　前端优化的途径有很多，按粒度大致可以分为两类，第一类是页面级别的优化，例如 HTTP请求数、脚本的无阻塞加载、内联脚本的位置优化等 ;第二类则是代码级别的优化，例如 Javascript中的DOM 操作优化、CSS选择符优化、图片优化以及 HTML结构优化等等。另外，本着提高投入产出比的目的，后文提到的各种优化策略大致按照投入产出比从大到小的顺序排列。

一、页面级优化
1. 减少 HTTP请求数
　　这条策略基本上所有前端人都知道，而且也是最重要最有效的。都说要减少 HTTP请求，那请求多了到底会怎么样呢 ?首先，每个请求都是有成本的，既包含时间成本也包含资源成本。一个完整的请求都需要经过 DNS寻址、与服务器建立连接、发送数据、等待服务器响应、接收数据这样一个 “漫长” 而复杂的过程。时间成本就是用户需要看到或者 “感受” 到这个资源是必须要等待这个过程结束的，资源上由于每个请求都需要携带数据，因此每个请求都需要占用带宽。另外，由于浏览器进行并发请求的请求数是有上限的 ，因此请求数多了以后，浏览器需要分批进行请求，因此会增加用户的等待时间，会给用户造成站点速度慢这样一个印象，即使可能用户能看到的第一屏的资源都已经请求完了，但是浏览器的进度条会一直存在。
　　减少 HTTP请求数的主要途径包括：

从设计实现层面简化页面

如果你的页面像百度首页一样简单，那么接下来的规则基本上都用不着了。保持页面简洁、减少资源的使用时最直接的。如果不是这样，你的页面需要华丽的皮肤，则继续阅读下面的内容。

合理设置 HTTP缓存

　　缓存的力量是强大的，恰当的缓存设置可以大大的减少 HTTP请求。以有啊首页为例，当浏览器没有缓存的时候访问一共会发出 78个请求，共 600多 K数据 (如图 1.1)，而当第二次访问即浏览器已缓存之后访问则仅有 10个请求，共 20多 K数据 (如图 1.2)。 (这里需要说明的是，如果直接 F5刷新页面的话效果是不一样的，这种情况下请求数还是一样，不过被缓存资源的请求服务器是 304响应，只有 Header没有Body ，可以节省带宽 )

　　怎样才算合理设置?原则很简单，能缓存越多越好，能缓存越久越好。例如，很少变化的图片资源可以直接通过 HTTP Header中的Expires设置一个很长的过期头 ;变化不频繁而又可能会变的资源可以使用 Last-Modifed来做请求验证。尽可能的让资源能够在缓存中待得更久。

资源合并与压缩
　　如果可以的话，尽可能的将外部的脚本、样式进行合并，多个合为一个。另外， CSS、 Javascript、Image 都可以用相应的工具进行压缩，压缩后往往能省下不少空间。

CSS Sprites
　　合并 CSS图片，减少请求数的又一个好办法。

Lazy Load Images
　　这条策略实际上并不一定能减少 HTTP请求数，但是却能在某些条件下或者页面刚加载时减少 HTTP请求数。对于图片而言，在页面刚加载的时候可以只加载第一屏，当用户继续往后滚屏的时候才加载后续的图片。这样一来，假如用户只对第一屏的内容感兴趣时，那剩余的图片请求就都节省了。 有啊首页 曾经的做法是在加载的时候把第一屏之后的图片地址缓存在 Textarea标签中，待用户往下滚屏的时候才 “惰性” 加载

2. 脚本延迟加载
　　前文有谈到，浏览器是可以并发请求的，这一特点使得其能够更快的加载资源，然而外链脚本在加载时却会阻塞其他资源，例如在脚本加载完成之前，它后面的图片、样式以及其他脚本都处于阻塞状态，直到脚本加载完成后才会开始加载。如果将脚本放在比较靠前的位置，则会影响整个页面的加载速度从而影响用户体验。解决这一问题的方法有很多。

将脚本尽可能的往后挪，减少对并发下载的影响。
使用 script元素的defer 属性(存在兼容性问题和其他一些问题，例如不能使用 document.write)
使用setTimeout
在HTML5中引入了 Web Workers的机制，恰恰可以解决此类问题。
Lazy Load Javascript（只有在需要加载的时候加载，在一般情况下并不加载信息内容。）
　　随着 Javascript框架的流行，越来越多的站点也使用起了框架。不过，一个框架往往包括了很多的功能实现，这些功能并不是每一个页面都需要的，如果下载了不需要的脚本则算得上是一种资源浪费 -既浪费了带宽又浪费了执行花费的时间。目前的做法大概有两种，一种是为那些流量特别大的页面专门定制一个专用的 mini版框架，另一种则是 Lazy Load。YUI 则使用了第二种方式，在 YUI的实现中，最初只加载核心模块，其他模块可以等到需要使用的时候才加载。
3. 将 CSS放在 HEAD中
　　如果将 CSS放在其他地方比如 BODY中，则浏览器有可能还未下载和解析到 CSS就已经开始渲染页面了，这就导致页面由无 CSS状态跳转到 CSS状态，用户体验比较糟糕。除此之外，有些浏览器会在 CSS下载完成后才开始渲染页面，如果 CSS放在靠下的位置则会导致浏览器将渲染时间推迟。

4. 异步请求 Callback（就是将一些行为样式提取出来，慢慢的加载信息的内容）
　　在某些页面中可能存在这样一种需求，需要使用 script标签来异步的请求数据。类似：

/*Callback 函数*/ 
function myCallback(info){ 
//do something here 
}
像以上这种方式直接在页面上写脚本对页面的性能也是有影响的，即增加了页面首次加载的负担，推迟了 DOMLoaded和window.onload 事件的触发时机。如果时效性允许的话，可以考虑在 DOMLoaded事件触发的时候加载，或者使用 setTimeout方式来灵活的控制加载的时机。

5. 减少不必要的 HTTP跳转
　　对于以目录形式访问的 HTTP链接，很多人都会忽略链接最后是否带 ’/‘，
假如你的服务器对此是区别对待的话，那么你也需要注意，这其中很可能隐藏了 301跳转，增加了多余请求。其中第一个链接是以无 ’/‘结尾的方式访问的，于是服务器有了一次跳转。

6. 避免重复的资源请求
　　这种情况主要是由于疏忽或页面由多个模块拼接而成，然后每个模块中请求了同样的资源时，会导致资源的重复请求

7. 图片设定不响应重绘的尺寸，
如果你的不设定尺寸、同时外部容器没有定死高宽，则图片在首次载入时候，占据空间会从0到完全出现，左右上下都可能位移，发生大规模的重绘。

二、代码级优化
1. Javascript
DOM
　　DOM操作应该是脚本中最耗性能的一类操作，例如增加、修改、删除 DOM元素或者对 DOM集合进行操作。如果脚本中包含了大量的 DOM操作则需要注意以下几点：
a. HTML Collection（HTML收集器，返回的是一个数组内容信息）
　　在脚本中 document.images、document.forms 、getElementsByTagName()返回的都是 HTMLCollection类型的集合，在平时使用的时候大多将它作为数组来使用，因为它有 length属性，也可以使用索引访问每一个元素。不过在访问性能上则比数组要差很多，原因是这个集合并不是一个静态的结果，它表示的仅仅是一个特定的查询，每次访问该集合时都会重新执行这个查询从而更新查询结果。所谓的 “访问集合” 包括读取集合的 length属性、访问集合中的元素。
　　因此，当你需要遍历 HTML Collection的时候，尽量将它转为数组后再访问，以提高性能。即使不转换为数组，也请尽可能少的访问它，例如在遍历的时候可以将 length属性、成员保存到局部变量后再使用局部变量。

b. Reflow & Repaint
除了上面一点之外， DOM操作还需要考虑浏览器的 Reflow和Repaint ，因为这些都是需要消耗资源的。CSS通配符*，CSS选择器层叠不能超过三层，CSS尽量使用类选择器，书写HTML少使用table，结构要尽量简单-DOM树要小….等这些忠告，以前我就大概知道使用通配符或者CSS选择器层次过多可能会降低性能，至于为什么不使用table标签我一直是迷迷糊糊，具体的可以参加以下文章：

Repaint 、Reflow 的基本认识和优化

localStorage本地存储与优化。
localStorage有两种使用场景：
一是对于大数据量交互，数据不怎么更新的，含版本控制机制，一次请求，之后高枕无忧；
Untitled Image

Untitled Image

二是代替cookie实现某些功能，带过期时间管理，降低页面cookie大小（IE6-7依然使用Cookie）。

慎用 with
with(obj){ p = 1}; 代码块的行为实际上是修改了代码块中的 执行环境 ，将obj放在了其作用域链的最前端，在 with代码块中访问非局部变量是都是先从 obj上开始查找，如果没有再依次按作用域链向上查找，因此使用 with相当于增加了作用域链长度。而每次查找作用域链都是要消耗时间的，过长的作用域链会导致查找性能下降。
　　因此，除非你能肯定在 with代码中只访问 obj中的属性，否则慎用 with，替代的可以使用局部变量缓存需要访问的属性。

避免使用 eval和 Function
每次 eval 或 Function 构造函数作用于字符串表示的源代码时，脚本引擎都需要将源代码转换成可执行代码。这是很消耗资源的操作 —— 通常比简单的函数调用慢 100倍以上。
　　eval 函数效率特别低，由于事先无法知晓传给 eval 的字符串中的内容，eval在其上下文中解释要处理的代码，也就是说编译器无法优化上下文，因此只能有浏览器在运行时解释代码。这对性能影响很大。
　　Function 构造函数比 eval略好，因为使用此代码不会影响周围代码 ;但其速度仍很慢。
　　此外，使用 eval和 Function也不利于Javascript 压缩工具执行压缩。

减少作用域链查找（这方面设计到一些内容的相关问题）
　　前文谈到了作用域链查找问题，这一点在循环中是尤其需要注意的问题。如果在循环中需要访问非本作用域下的变量时请在遍历之前用局部变量缓存该变量，并在遍历结束后再重写那个变量，这一点对全局变量尤其重要，因为全局变量处于作用域链的最顶端，访问时的查找次数是最多的。

此外，要减少作用域链查找还应该减少闭包的使用。

字符串拼接
　　在 Javascript中使用”+” 号来拼接字符串效率是比较低的，因为每次运行都会开辟新的内存并生成新的字符串变量，然后将拼接结果赋值给新变量。与之相比更为高效的做法是使用数组的 join方法，即将需要拼接的字符串放在数组中最后调用其 join方法得到结果。不过由于使用数组也有一定的开销，因此当需要拼接的字符串较多的时候可以考虑用此方法。
2. CSS选择符
　　在大多数人的观念中，都觉得浏览器对 CSS选择符的解析式从左往右进行的，例如

#toc A { color: #444; }
　　这样一个选择符，如果是从左往右解析则效率会很高，因为第一个 ID选择基本上就把查找的范围限定了，但实际上浏览器对选择符的解析是从右往左进行的。如上面的选择符，浏览器必须遍历查找每一个 A标签的祖先节点，效率并不像之前想象的那样高。

