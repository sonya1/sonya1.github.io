---
layout: post
title: 正则RegExp与String的方法
category: JS
tags: RegExp与String
keywords: RegExp与String
description: RegExp与String
---

正则表达式的方法：
```javascript
reg.test( str );  //true or false
reg.exec( str );  //返回匹配值（一个数组[匹配值，分组，index:匹配值的开始位置，input:str值]），没找到返回null。
```

字符串中有关正则的方法：
```javascript
str.search( reg );  //返回找到的开始位置,没找到-1,找到即返回，所以无需g全局
str.replace( reg , newStr );  
str.match( reg );  //返回匹配值组成的数组 str.match();  -> returns [""]
str.split( reg );  
```

test的用法和exec一致，只不过返回值是 true false。

## 正则表达式对象有两个定义方式:
- 1、第一种定义：

new RegExp(pattern, attributes);
```javascript
var reg = new RegExp(“abc”,”g”)
```

其中pattern为表示表达式内容,如上表示匹配abc

attributes：g,全局匹配，i不区分大小写，m执行多行匹配，用最多的为g和i

- 2、第二种定义:/pattern/attributes.

如：var reg = /abc/g;

## exec和match的区别：
- 1.exec是正则表达式的方法，而不是字符串的方法，它的参数才是字符串，如下所示：

```javascript
var reg = new RegExp("abc") ; 
var str = "3abc4，5abc6";
reg.exec(str ); 
``` 

- 2. match是字符串执行匹配正则表达式规则的方法，他的参数是正则表达式，如
```javascript
var reg = new RegExp("abc") ; 
var str = "3abc4,5abc6";
str.match(reg);
```

当正则表达式为非全局匹配,exec和match的作用是一样的：
```javascript
var reg = new RegExp("a(bc)") ; 
var str = "3abc4,5abc6";
console.log(reg.exec(str));//["abc", "bc", index: 1, input: "3abc4,5abc6"]
console.log(str.match(reg));//["abc", "bc", index: 1, input: "3abc4,5abc6"]
```

你会发现两者执行的结果都是：abc,bc;

上面例子的数组长度，是2。index和input只是数组属性。

除了数组元素和 length 属性之外，exec() 方法还返回两个属性。

index 属性声明的是匹配文本的第一个字符的位置。

input 属性则存放的是被检索的字符串 string。

### 我们可以看得出，在调用非全局的 RegExp 对象的 exec() 方法时，返回的数组与调用方法 String.match() 返回的数组是相同的。

## 当正则表达式为全局匹配,exec和match存在区别：
如果是全局匹配，exec() 的工作原理如下：

- 1. 找到第一个 "you"，并存储其位置
- 2. 如果再次运行 exec()，则从存储的位置（lastIndex）开始检索，并找到下一个 "you"，并存储其位置

当 RegExpObject 是一个全局正则表达式时，exec() 的行为就稍微复杂一些。它会在 RegExpObject 的 lastIndex 属性指定的字符处开始检索字符串 string。当 exec() 找到了与表达式相匹配的文本时，在匹配后，它将把 RegExpObject 的 lastIndex 属性设置为匹配文本的最后一个字符的下一个位置。

这就是说，我们可以通过反复调用 exec() 方法来遍历字符串中的所有匹配文本。当 exec() 再也找不到匹配的文本时，它将返回 null，并把 lastIndex 属性重置为 0。这里引入lastIndex属性，这货只有跟g和test（或者g和exec）三者搭配时才有作用。它是pattern的一个属性，一个整数，标示开始下一次匹配的字符位置。
```javascript
var reg = new RegExp("a(bc)","g") ; 
var str = "3abc4,5abc6";
do {
  ans = reg.exec(s);
  console.log(ans);
  console.log('lastIndex:' + reg.lastIndex);
}while (ans !== null)

结果是

["abc", "bc", index: 1, input: "3abc4,5abc6"]
lastIndex:4
["abc", "bc", index: 7, input: "3abc4,5abc6"]
lastIndex:10
null
lastIndex:0
```

当使用全局模式匹配时，match直接返回一个字符串数组,获得的信息远没有exec多，但是使用方式简单。

实例如下：
```javascript
var reg = new RegExp("a(bc)","g") ; 
var str = "3abc4,5abc6";
console.log(reg.exec(str));
console.log(str.match(reg));
则两者返回的结果是abc,bc和abc,abc,

["abc", "bc", index: 1, input: "3abc4,5abc6"]
["abc", "abc"]
```

## 正则的例子
1.匹配账号是否合法（字母开头，允许5-16位，允许字母数字下划线）
```js
^[a-zA-Z][\w]{4-15}$
```

2.将姓名first与last反过来
```js
var re = /(\w+)\s(\w+)/;
var str = "John Smith";
var newStr = str.replace(re,"$2 $1");
console.log(newStr);  //"Smith John"
```


## 总结

当正则表达式定义为非全局匹配时，exec和match执行的结果是一样，均返回第一个匹配的字符串内容,返回数组
["abc", "bc", index: 1, input: "3abc4,5abc6"]；

当正则表达式定义为全局匹配时，exec和match执行，若存在多处匹配内容，则match返回的是多个元素数组,exec只会找到一个匹配的（lastIndex）；

也就说，exec与全局是否定义无关系，而match则于全局相关联，当定义为非全局，两者执行结果相同；

参考：[媛媛的小窝——js正则表达test、exec和match的区别](http://www.zyy1217.com/2016/12/29/%20js%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BEexec%E5%92%8Cmatch%E7%9A%84%E5%8C%BA%E5%88%AB/)
