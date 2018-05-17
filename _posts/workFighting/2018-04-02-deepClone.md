---
layout: post
title: Javascript深克隆原理与实现
category: workFighting
tags: Javascript深克隆
keywords: Javascript深克隆
description: Javascript深克隆原理与实现
---

一. js中的对象
谈到对象的克隆，必定要说一下对象的概念。

js中的数据类型分为两大类：原始类型和对象类型。

原始类型包括：数值number、字符串string、布尔值boolean、null、undefined.基本数据类型保存在栈中，
对象类型包括：对象即是属性的集合，当然这里又有两个特殊的对象—-函数Array、数组（键值的有序集合）。引用数据类型的值是对象，保存在堆中。
这两种类型在复制克隆的时候是有很大区别的。原始类型存储的是对象的实际数据，而对象类型存储的是对象的引用地址（对象的实际内容单独存放，为了减少数据开销通常存放在内存中

这两种数据类型存储方式有很大区别，尤其是在参数传递上。可以看看下面的例子。

参数的传递
Javascript访问变量有按值传递与按引用传递，但是参数只能按值传递

举例如下：

声明一个对象person，它是一个引用类型，函数传递的参数是person对象的值，也就是他的内存地址，

function setName(obj)
{ 
  obj.name="我是传递的"; 
  obj=new Object(); 
  obj.name="我是new出来的"; 
} 
var person=new Object(); 
setName(person); 
console.log(person.name);
以上代码的弹出值是:我是传递的，很多人可能会以为将会弹出“我是new出来的”，下面进行一下简单的分析:

在函数外面创建一个对象，并将对象的引用赋值给变量person，person中存储的是对象在内存中的存储地址，当为函数传递参数时，就是传递的在函数外面创建的对象的地址。在函数中，为外面创建的对象创建一个自定义属性name，然后又创建一个新的对象，并将新对象的地址赋值给obj，这个时候obj指向的并不是函数外面创建的对象，所以外面对象name属性不会被改变。

总的来说，就是函数内创建object对象不会改变外面的属性值

二、克隆的概念
浅度克隆：原始类型为值传递，对象类型仍为引用传递。

深度克隆：所有元素或属性均完全复制，与原对象完全脱离，也就是说所有对于新对象的修改都不会反映到原对象中。

三、浅克隆的表现
对于原始类型即使我们采用普通的克隆方式仍能得到正确的结果，原因就是原始类型存储的是对象的实际数据。

对象类型

简单的循环遍历无法对复杂嵌套对象进行深度克隆，（函数除外）。因此需要递归循环遍历，实现深克隆

四、深克隆的实现
　　为了保证对象的所有属性都被复制到，我们必须知道如果for循环以后，得到的元素仍是Object或者Array，那么需要再次循环，直到元素是原始类型或者函数为止。为了得到元素的类型，我们定义一个通用函数，用来返回传入对象的类型。

 //返回传递给他的任意对象的类
function isClass(o){
    if(o===null) return "Null";
    if(o===undefined) return "Undefined";
    return Object.prototype.toString.call(o).slice(8,-1);
}
为什么不直接用toString方法？这是为了防止对象中的toString方法被重写，为了正确的调用toString()版本，必须间接的调用Function.call()方法

为什么不使用typeof来直接判断类型？因为对于Array而言，使用typeof（Array）返回的是object，所以不能得到正确的Array，这里对于后续的数组克隆将产生致命的问题。

确定是那种基本数据类型用typeof,确定是哪种引用数据类型用instanceof

//深度克隆
function deepClone(obj){
    var result,oClass=isClass(obj);
        //确定result的类型
    if(oClass==="Object"){
        result={};
    }else if(oClass==="Array"){
        result=[];
    }else{
        return obj;
    }
    for(key in obj){
        var copy=obj[key];
        if(isClass(copy)=="Object" || isClass(copy)=="Array"){
            result[key]=arguments.callee(copy);//递归调用
        }else{
            result[key]=obj[key];
        }
    }
    return result;
}
//返回传递给他的任意对象的类
function isClass(o){
    if(o===null) return "Null";
    if(o===undefined) return "Undefined";
    return Object.prototype.toString.call(o).slice(8,-1);
}
var oPerson={
    oName:"rookiebob",
    oAge:"18",
    oAddress:{
        province:"beijing"
    },    
    ofavorite:[
        "swimming",
        {reading:"history book"}
    ],
    skill:function(){
        console.log("bob is coding");
    }
};
//深度克隆一个对象
var oNew=deepClone(oPerson);
 
oNew.ofavorite[1].reading="picture";
console.log(oNew.ofavorite[1].reading);//picture
console.log(oPerson.ofavorite[1].reading);//history book
 
oNew.oAddress.province="shanghai";
console.log(oPerson.oAddress.province);//beijing
console.log(oNew.oAddress.province);//shanghai
参考知乎上的答案javascript中的深拷贝和浅拷贝？

不过这段代码只能克隆变量，函数，数组与JSON对象。与jquery的深克隆机制相同，无法正确深复制 JSON 对象以外的对象。

第三方库：
jQuery —— $.extend()
在jQuery中可以通过 .extend()方法来完成深复制，调用.extend(true, {}, …)就可以实现深复制，参考下面的例子：

var x = {
    a: 1,
    b: { f: { g: 1 } },
    c: [ 1, 2, 3 ]
};
var y = $.extend({}, x),          //shallow copy
    z = $.extend(true, {}, x);    //deep copy
y.b.f === x.b.f       // true
z.b.f === x.b.f       // false
在jQuery的源码 - src/core.js #L121 可以找到$.extend()源码的实现

lodash —— .clone() / .cloneDeep()
在lodash中关于复制的方法有两个，分别是.clone()和.cloneDeep()。其中.clone(obj, true)等价于.cloneDeep(obj)。使用上，lodash和前两者并没有太大的区别，但看了源码会发现， jQuery 不过60多行，可 lodash 中与深复制相关的代码却有上百行，这是什么道理呢？

var $ = require("jquery"),
    _ = require("lodash");
var arr = new Int16Array(5),
    obj = { a: arr },
    obj2;
arr[0] = 5;
arr[1] = 6;
// 1. jQuery
obj2 = $.extend(true, {}, obj);
console.log(obj2.a);                            // [5, 6, 0, 0, 0]
Object.prototype.toString.call(obj2);           // [object Int16Array]
obj2.a[0] = 100;
console.log(obj);                               // [100, 6, 0, 0, 0]
//此处jQuery不能正确处理Int16Array的深复制！！！
// 2. lodash
obj2 = _.cloneDeep(obj);                       
console.log(obj2.a);                            // [5, 6, 0, 0, 0]
Object.prototype.toString.call(arr2);           // [object Int16Array]
obj2.a[0] = 100;
console.log(obj);                               // [5, 6, 0, 0, 0]
通过上面这个例子可以初见端倪，jQuery 无法正确深复制 JSON 对象以外的对象，而我们可以从下面这段代码片段可以看出 lodash 花了大量的代码来实现 ES6 引入的大量新的标准对象。更厉害的是，lodash 针对存在循环引用的对象的处理也是非常出色的。因此相较而言，lodash 在深复制上的行为反馈比前两个库好很多，是更拥抱未来的一个第三方库。
