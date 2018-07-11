---
layout: post
title: 前端模块化
category: workFighting
tags: 前端模块化
keywords: CommonJS，AMD，CMD
description: CommonJS，AMD，CMD
---

原文来自：[媛媛的小窝——前端模块化](http://www.zyy1217.com/2016/12/30/%E5%89%8D%E7%AB%AF%E6%A8%A1%E5%9D%97%E5%8C%96/)

## 前端模块化
在JavaScript发展初期就是为了实现简单的页面交互逻辑，寥寥数语即可；如今CPU、浏览器性能得到了极大的提升，很多页面逻辑迁移到了客户端（表单验证等），随着web2.0时代的到来，Ajax技术得到广泛应用，jQuery等前端库层出不穷，前端代码日益膨胀。

这时候JavaScript作为嵌入式的脚本语言的定位动摇了，JavaScript却没有为组织代码提供任何明显帮助，甚至没有类的概念，更不用说模块（module）了，JavaScript极其简单的代码组织规范不足以驾驭如此庞大规模的代码。

## 模块
### 函数封装
我们在讲函数的时候提到，函数一个功能就是实现特定逻辑的一组语句打包，而且JavaScript的作用域就是基于函数的，所以把函数作为模块化的第一步是很自然的事情，在一个文件里面编写几个相关函数就是最开始的模块了
```js
function fn1(){
    statement
}
function fn2(){
    statement
}
```
这样以后加载函数所在文件，调用函数就可以了

这种做法的缺点很明显：污染了全局变量，无法保证不与其他模块发生变量名冲突，而且模块成员之间没什么关系。

### 对象
为了解决上面问题，对象的写法应运而生，可以把所有的模块成员封装在一个对象中
```js
var myModule = {
    var1: 1,
    var2: 2,
    fn1: function(){
    },
    fn2: function(){
    }
}
```
这样我们在希望调用模块的时候引用对应文件，然后myModule.fn2();这样避免了变量污染，只要保证模块名唯一即可，同时同一模块内的成员也有了关系

看似不错的解决方案，但是也有缺陷，外部可以随意修改内部成员myModel.var1 = 100;这样就会产生意外的安全问题.

### 立即执行函数
可以通过立即执行函数，来达到隐藏细节的目的
```js
var myModule = (function(){
    var var1 = 1;
    var var2 = 2;
    function fn1(){
    }
    function fn2(){
    }
    return {
        fn1: fn1,
        fn2: fn2
    };
})();
```
这样在模块外部无法修改我们没有暴露出来的变量、函数

上述做法就是我们模块化的基础，目前，通行的JavaScript模块规范主要有两种：CommonJS和AMD

## CommonJS
我们先从CommonJS谈起，因为在网页端没有模块化编程只是页面JavaScript逻辑复杂，但也可以工作下去，在服务器端却一定要有模块，所以虽然JavaScript在web端发展这么多年，第一个流行的模块化规范却由服务器端的JavaScript应用带来，CommonJS规范是由NodeJS发扬光大，这标志着JavaScript模块化编程正式登上舞台。

### 定义模块
根据CommonJS规范，一个单独的文件就是一个模块。每一个模块都是一个单独的作用域，也就是说，在该模块内部定义的变量，无法被其他模块读取，除非定义为global对象的属性.

### 模块输出：
模块只有一个出口，module.exports对象，我们需要把模块希望输出的内容放入该对象

### 加载模块：
加载模块使用require方法，该方法读取一个文件并执行，返回文件内部的module.exports对象

看个例子
```js
//模块定义 myModel.js
var name = 'Byron';
function printName(){
    console.log(name);
}
function printFullName(firstName){
    console.log(firstName + name);
}
module.exports = {
    printName: printName,
    printFullName: printFullName
}
//加载模块
var nameModule = require('./myModel.js');
nameModule.printName();
```
不同的实现对require时的路径有不同要求，一般情况可以省略js拓展名，可以使用相对路径，也可以使用绝对路径，甚至可以省略路径直接使用模块名（前提是该模块是系统内置模块）

### 尴尬的浏览器
仔细看上面的代码，会发现require是同步的。模块系统需要同步读取模块文件内容，并编译执行以得到模块接口。

这在服务器端实现很简单，也很自然，然而，想在浏览器端实现问题却很多。

浏览器端，加载JavaScript最佳、最容易的方式是在document中插入script 标签。但脚本标签天生异步，传统CommonJS模块在浏览器环境中无法正常加载。

优点：
1、简单并容易使用

2、服务器端模块便于重用

### 缺点:

1、同步的模块加载方式不适合在浏览器环境中，同步意味着阻塞加载，浏览器资源是异步加载的

2、不能非阻塞的并行加载多个模块

### module.exports与exports的区别
exports 是指向的 module.exports 的引用

module.exports 初始值为一个空对象 {}，所以 exports 初始值也是 {}

require() 返回的是 module.exports 而不是 exports

```js
exports示例：
// app.js
var circle = require('./circle');
console.log(circle.area(4));
// circle.js
exports.area = function(r){
return r * r * Math.PI;
}
module.exports示例：

// app.js
var area = require('./area');
console.log(area(4));
// area.js
module.exports = function(r){
return r * r * Math.PI;
}
错误的情况：

// app.js
var area = require('./area');
console.log(area(4));
// area.js
exports = function(r){
return r * r * Math.PI;
}
```
其实是对 exports 进行了覆盖，也就是说 exports 指向了一块新的内存（内容为一个计算圆面积的函数），也就是说 exports 和 module.exports 不再指向同一块内存，也就是说此时 exports 和 module.exports 毫无联系，也就是说 module.exports 指向的那块内存并没有做任何改变，仍然为一个空对象{}，也就是说area.js导出了一个空对象，所以我们在 app.js 中调用 area(4) 会报 TypeError: object is not a function 的错误。

总结：
当我们想让模块导出的是一个对象时， exports 和 module.exports 均可使用（但 exports 也不能重新覆盖为一个新的对象），而当我们想导出非对象接口时，就必须也只能覆盖 module.exports 。

## AMD
AMD 即Asynchronous Module Definition，中文名是异步模块定义的意思。它是一个在浏览器端模块化开发的规范

由于不是JavaScript原生支持，使用AMD规范进行页面开发需要用到对应的库函数，也就是大名鼎鼎RequireJS，实际上AMD 是 RequireJS 在推广过程中对模块定义的规范化的产出。

### requireJS主要解决两个问题

多个js文件可能有依赖关系，被依赖的文件需要早于依赖它的文件加载到浏览器.js加载的时候浏览器会停止页面渲染，加载文件越多，页面失去响应时间越长.
看一个使用requireJS的例子
```js
 // 定义模块 myModule.js
define(['dependency'], function(){
    var name = 'Byron';
    function printName(){
        console.log(name);
    }
    return {
        printName: printName
    };
});
// 加载模块
require(['myModule'], function (my){
　 my.printName();
});
```

### 语法
requireJS定义了一个函数 define，它是全局变量，用来定义模块
```js
define(id?, dependencies?, factory);

id：可选参数，用来定义模块的标识，如果没有提供该参数，脚本文件名（去掉拓展名）

dependencies：是一个当前模块依赖的模块名称数组

factory：工厂方法，模块初始化要执行的函数或对象。如果为函数，它应该只被执行一次。如果是对象，此对象应该为模块的输出值

在页面上使用require函数加载模块
require([dependencies], function(){});

require()函数接受两个参数

第一个参数是一个数组，表示所依赖的模块

第二个参数是一个回调函数，当前面指定的模块都加载成功后，它将被调用。加载的模块会以参数形式传入该函数，从而在回调函数内部就可以使用这些模块

require()函数在加载依赖的函数的时候是异步加载的，这样浏览器不会失去响应，它指定的回调函数，只有前面的模块都加载成功后，才会运行，解决了依赖性的问题。
```

## CMD
CMD 即Common Module Definition通用模块定义，CMD规范是国内发展出来的，就像AMD有个requireJS，CMD有个浏览器的实现SeaJS，SeaJS要解决的问题和requireJS一样，只不过在模块定义方式和模块加载（可以说运行、解析）时机上有所不同

### 语法
Sea.js 推崇一个模块一个文件，遵循统一的写法
```js
define
define(id?, deps?, factory)
```
因为CMD推崇一个文件一个模块，所以经常就用文件名作为模块id.

CMD推崇依赖就近，所以一般不在define的参数中写依赖，在factory中写
```js
factory有三个参数
function(require, exports, module)

require 是 factory 函数的第一个参数:require(id)

require 是一个方法，接受 模块标识 作为唯一参数，用来获取其他模块提供的接口

exports 是一个对象，用来向外提供模块接口

module 是一个对象，上面存储了与当前模块相关联的一些属性和方法
```

demo
```js
// 定义模块  myModule.js
define(function(require, exports, module) {
  var $ = require('jquery.js')
  $('div').addClass('active');
});
// 加载模块
seajs.use(['myModule.js'], function(my){
});
```

## AMD与CMD区别：
最明显的区别就是在模块定义时对依赖的处理不同

AMD推崇依赖前置，在定义模块的时候就要声明其依赖的模块

CMD推崇就近依赖，只有在用到某个模块的时候再去require

对于依赖的模块，AMD 是提前执行，CMD 是延迟执行。不过 RequireJS 从 2.0 开始，也改成可以延迟执行（根据写法不同，处理方式不同）。CMD 推崇 as lazy as possible.

这种区别各有优劣，只是语法上的差距，而且requireJS和SeaJS都支持对方的写法

AMD和CMD最大的区别是对依赖模块的执行时机处理不同，注意不是加载的时机或者方式不同

很多人说requireJS是异步加载模块，SeaJS是同步加载模块，这么理解实际上是不准确的，其实加载模块都是异步的，只不过AMD依赖前置，js可以方便知道依赖模块是谁，立即加载，而CMD就近依赖，需要使用把模块变为字符串解析一遍才知道依赖了那些模块，这也是很多人诟病CMD的一点，牺牲性能来带来开发的便利性，实际上解析模块用的时间短到可以忽略。

对于依赖的模块，AMD 是提前执行，CMD 是延迟执行。不过 RequireJS 从 2.0 开始，也改成可以延迟执行（根据写法不同，处理方式不同）。CMD 推崇 as lazy as possible.

CMD 推崇依赖就近，AMD 推崇依赖前置。看代码：
```js
// CMD
define(function(require, exports, module) {
    var a = require('./a')
    a.doSomething()
    // 此处略去 100 行
    var b = require('./b') // 依赖可以就近书写
    b.doSomething()
    // ...
})
// AMD 默认推荐的是
define(['./a', './b'], function(a, b) { // 依赖必须一开始就写好
    a.doSomething()
    // 此处略去 100 行
    b.doSomething()
    // ...
})
```
虽然 AMD 也支持 CMD 的写法，同时还支持将 require 作为依赖项传递，但 RequireJS 的作者默认是最喜欢上面的写法，也是官方文档里默认的模块定义写法。

AMD 的 API 默认是一个当多个用，CMD 的 API 严格区分，推崇职责单一。比如 AMD 里，require 分全局 require 和局部 require，都叫 require。CMD 里，没有全局 require，而是根据模块系统的完备性，提供 seajs.use 来实现模块系统的加载启动。CMD 里，每个 API 都简单纯粹。

还有一些细节差异，具体看这个规范的定义就好，就不多说了。

同样都是异步加载模块，AMD在加载模块完成后就会执行该模块，（不过 RequireJS 从 2.0 开始，也改成可以延迟执行。）所有模块都加载执行完后会进入require的回调函数，执行主逻辑，这样的效果就是依赖模块的执行顺序和书写顺序不一定一致，看网络速度，哪个先下载下来，哪个先执行，但是主逻辑一定在所有依赖加载完成后才执行

CMD加载完某个依赖模块后并不执行，只是下载而已，在所有依赖模块加载完成后进入主逻辑，遇到require语句的时候才执行对应的模块，这样模块的执行顺序和书写顺序是完全一致的

这也是很多人说AMD用户体验好，因为没有延迟，依赖模块提前执行了，CMD性能好，因为只有用户需要的时候才执行的原因.


