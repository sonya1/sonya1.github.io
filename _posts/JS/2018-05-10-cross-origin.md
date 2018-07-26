---
layout: post
title: 跨域大全
category: JS
tags: 跨域
keywords: 跨域
description: 
---

## 什么是跨域
| URL | 说明 | 是否允许通信 |   
| - | :-: | -: |   
| http://www.a.com/a.js，http://www.a.com/b.js | 同一域名下| 允许 |   
| http://www.a.com/lab/a.js，http://www.a.com/scripts/b.js | 同一域名下不同文件夹| 允许 |  
| http://www.a.com：8000/a.js，http://www.a.com/b.js | 同一域名，不同端口| 不允许（默认端口80） |  
| http://www.a.com/a.js，https://www.a.com/b.js | 同一域名，不同协议| 不允许 |   
| http://www.a.com/a.js，http://70.32.92.74/b.js | 域名和域名对应IP| 不允许 |   
| http://www.a.com/a.js，http://script.a.com/b.js | 主域相同，子域不同| 不允许 |   
| http://www.a.com/a.js，http://a.com/b.js | 同一域名,不同二级域名| 不允许（cookie这种情况下也不允许访问） |   
| http://www.cnblogs.com/a.js，http://a.com/b.js | 不同域名| 不允许 |   

协议，域名，端口必须一样才可以！  

协议和端口造成的跨域问题，“前台”是无能为力的。

## 1.JSONP：借助script发送HTTP请求
  script元素可以作为一种AJAX传输机制：只须设置script元素的src属性（假如他还没插入到document中，需要插入进去），然后浏览器就会发送一个HTTP请求以下载src属性所指向的URL。使用script元素进行Ajax传输的一个主要原因是,它不受同源策略的影响，因此可以使用它从其他的服务器请求数据，第二个原因是包含JSON编码数据的响应体会自动解码（执行）。  
    
  这种使用script元素作为Ajax传输的技术叫JSONP，若HTTP请求所得到的响应数据是经过JSON编码的，则适合使用该技术。P代表“填充”或“前缀”。  
  
  假设你已经写了一个服务，他处理GET请求并返回JSON编码的数据。同源的文档可以在代码中使用XMLHttpRequest和JSON.parse()。假设在服务器上启用了CORS，在新的浏览器下，跨域的文档也可以使用XMLHttpRequest享受到该服务。在不支持CORS的旧的浏览器下，跨域文档只能通过script元素访问这个服务。使用JSONP，JSON响应数据（理论上）适合放的JS代码，当他到达浏览器将执行他。相反，不使用jSONP,而是对JSON编码过的数据解码，结果还是数据，并没有做任何事情。  
  
  这就是JSONP中P的意义所在。当通过script元素调用数据时，响应内容必须用JavaScript函数名和圆括号包裹起来。而不是发送这样一段JSON数据：[1,2,{"name":"sonya"}]。他会发送这样一个包裹后的JSON响应：
  ```javascript
  handleResponse(
    [1,2,{"name":"sonya"}]
  )
  ```
  
  包裹后的响应会成为script元素的内容，他先判断JSON编码后的数据（毕竟就是一个javascript表达式），然后把他传给handleResponse()函数，我们可以假设，文档会拿这些数据做一些有用的事情。  
  
  为了可行起见，我们必须通过某种方式告诉服务，它正在从一个script元素调用，必须返回一个JSONP响应，而不应该是普通的JSON响应。这个可以通过在URL中添加一个查询参数来实现：例如，追加“?json”(或&json)。  
  
  在实践中，支持JSONP的服务不会强制指定客户端必须实现的回调函数名称，比如handleResponse。相反，它们使用查询参数的值，允许客户端指定一个函数名，然后使用函数名去填充响应。（见下例）许多支持JSONP的服务都能分辨出这个参数名。另一个常见的参数名称是callback，为了让使用到的服务支持类似特殊的需求，就需要在代码上做一些修改了。  
  
 简单实现：
  ```js
  function jsonp(url, jsonpCallback, success) {
     let script = document.createElement("script");
     script.src = url;
     script.async = true;
     script.type = "text/javascript";
     window[jsonpCallback] = function(data) {
       success & success(data);
     };
     document.body.appendChild(script);
}

jsonp(
   "http://xxx",
   "callback",
   function(value) {
     console.log(value);
   }
);
  ```
  
  ```javascript
  //根据指定的url发送一个JSONP请求
//然后把解析得到的响应数据传递给回调函数
//在URL中添加一个名为jsonp的查询参数，用于指定该请求的回调函数的名称
function getJSONP(url,callback){
  //为本次请求创建唯一的回调函数名称
  var cbnum = "cb" + getJSONP.counter++;  //每次自增计数器
  var cbname = "getJSONP." + cbnum;  //作为JSONP函数的属性

  //将回调函数名称以表单编码的形式添加到URL的查询部分中
  //使用jsonp作为参数名，一些支持JSONP的服务
  //可能使用其他的参数名，比如callback
  if(url.indexOf("?") === -1){  //url没有查询部分
    url += "?jsonp=" + cbname;  //作为查询部分添加参数
  }else{
    url += "&jsonp=" + cbname;  //作为新的参数添加它
  }

  //创建script元素用于发送请求
  var script = document.createElement("script");

  //定义将脚本执行的回调函数
  getJSONP[cbname] = function(response){
    try{
      callback(response);  //处理响应数据
    }finally{  //即使回调函数或响应抛出错误
      delete getJSONP[cbname];  //删除该函数
      script.parentNode.removeChild(script);  //移除script元素
    }
  };

  script.src = url;  //设置脚本的URL
  document.body.appendChild(script);  //把它添加到文档中
}

getJSONP.counter = 0;  //用于创建唯一回调函数名称的计数器
  ```

  创建一个新的script元素，设置其URL，并把它插入到文档中，正是该插入操作触发HTTP请求。  
  
  ——from《JS权威指南》
  
 #### ====================================================
  核心原理：html的script标签可以加载并执行其他域的JS文件。  
  JSONP原理：  
  1.首先在客户端注册一个callback函数，然后把callback的名字传给服务器。  
  2.服务器先生成json数据，然后以js语法的方式，生成一个function，function的名字就是客户端传过来的callback参数值，最后将json数据直接以入参的方式，放置到function中，这样就生成了一段js脚本，返回给客户端。  
  3.客户端解析script标签，并执行返回的js脚本，此时数据作为参数，传入到了客户端预先定义的callback函数里（动态执行回调函数）。  
  
  举个例子：
  客户端
  ```javascript
  <script>
    function doSomething(jsonData){
      //处理获得的json数据
    }
  </script>
  <script src="http://example.com/data.php?callback=doSomething"></script>
  ```
服务器端 data.php
  ```php
  <?php
    $callback = $_GET['callback'];
    $data = array(1,2,3);
    echo $callback."(".$data.")";
  ?>
  ```
  注意：链接要放在回调函数的下面。
  
### Jquery实现jsonp调用

```javascript
$(function(){
  $.ajax({
    type:"get",
    anysc:false,
    url:"http://example.com/flight.jsp?code=123",
    dataType:"jsonp",  //表示是jsonp类型
    jsonp:"callback",    //传递给请求处理程序或页面的，用以获得jsonp回调函数名的参数名（一般默认callback）
    jsonpCallback:"fightHandle",  //自定义的jsonp回调函数名称，默认为jquery自动生成的随机函数名
    success:function(json){
      alert("票价：",json.price);
    },
    error:function(){
      alert('error');
    }
  });
});
```
jQuery在处理jsonp类型的ajax时，自动帮你生成回调函数并把数据取出来供success属性方法来调用。

## 2.跨域资源共享 CORS（Cross-origin resource sharing）
它允许浏览器向跨源服务器发出XMLHttpRequest请求，从而克服了Ajax只能同源使用的限制。
### 2.1简介
CORS需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。  

整个CORS通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS通信与同源的Ajax通信没有差别，代码完全一样。浏览器一旦发现Ajax请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。  

因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。  
### 2.2两种请求
浏览器将CORS请求分为两类L简单请求和非简单请求。  

简单请求：  
1.请求方法是HEAD，GET，POST三种方法之一。  
2.HTTP的头信息不超出以下几种字段：Accept，Accept-Language，Content-Language，Last-Event-ID，Content-Type：只限于三个值：application/x-www-urlencoded，multipart/form-data，text/plain  

凡事不同时满足上面两个条件，就属于非简单请求。

浏览器对这两种请求的处理，是不一样的。

### 2.3 简单请求
对于简单请求，浏览器直接发出CORS请求。具体来说，就是在头信息中，增加一个Origin字段。
```http
GET /cors HTTP/1.1
Origin:http://api.bob.com
Host:api.alice.com
Accept-Language:en-US
Connection:keep-alive
User-Agent:Mozilla/5.0
```
上面的头信息中，Origin字段用来说明，本次请求来自哪个源（协议+域名+端口）。服务器根据这个值，决定是否同意这次请求。

如果Origin指定的源，不在许可范围内，服务器会返回一个正常的HTTP回应。浏览器发现，这个回应的头信息没有包含Access-Control-Allow-Origin字段，就知道出错了，从而抛出一个错误，呗XMLHttpRequest的onerror回调函数捕获。注意，这种错误无法通过状态码识别，因为HTTP回应的状态码有可能是200。

如果Origin指定的源在许可范围内，服务器返回的响应，会多出几个头信息字段。
```http
Access-Control-Allow-Origin:http://api.bob.com
Access-Control-Allow-Credentials:true
Access-Control-Expose-Headers:FooBar
Content-Type:text-html;charset=utf-8
```
上面的头信息中，有三个与CORS请求相关的字段，都以Access-Control-开头。

1.Access-Control-Allow-Origin：该字段是必须的。它的值要么是请求时Origin字段的值，要么是一个* ，表示接受任意域名的请求。

2.Access-Control-Allow-Credentials：该字段可选。它的值是一个布尔值，表示是否允许发送cookie。默认情况下，Cookie不包括在CORS请求中。设为true，即表示服务器明确许可，Cookie可以包含在请求中，一起发给服务器。这个值只能设为true，如果服务器不要浏览器发送cookie，删除该字段即可。

3.Access-Control-Expose-Headers：该字段可选。CORS请求时，XMLHttpRequest对象的getResponseHeader()方法只能拿到6个基本字段：Cache-Control，Content_language，Content-Type，Expires，Last-Modified，Pragma。如果想拿到其他字段，就必须在Access-Control-Expose-Headers里面指定。上面的例子指定，getResponseHeader("FooBar")可以返回FooBar字段的值。

### withCredentials属性
上面说到，CORS请求默认不发送Cookie和HTTP认证信息。如果要把Cookie发到服务器，一方面要服务器同意，指定Access-Control-Allow-Credentials：true，另一方面，开发者必须在Ajax请求中打开withCredentials属性。
```javascript
var xhr = new XMLHttpRequest();
xhr.withCredents = true;
```
否则，即使服务器同意发送Cookie，浏览器也不会发送。或者，服务器要求设置Cookie，浏览器也不会处理。

但是，如果省略withCredentials设置，有的浏览器还是会一起发送Cookie。这时，可以显示关闭withCredentials属性：xhr.withCredentials = false;

需要注意的是，如果要发送Cookie，Access-Control-Allow-Origin就不能设为* 号，必须指定明确的、与请求网页一致的域名。同时，Cookie依然遵循同源策略，只有用服务器域名设置的Cookie才会上传，其他域名的Cookie并不会上传，且（跨源）原网页代码中的document.cookie也无法读取服务器域名下的cookie。

### 2.4 非简单请求
#### 2.4.1 预检请求
非简单请求是那种对服务器有特殊要求的请求，比如请求方法是PUT或DELETE，或者Conten-Type字段的类型是application/json。

非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为“预检”请求。

浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单中，以及可以使用哪些HTTP动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的XMLHttpRequest请求，否则就报错。
```javascript
var url = "http://api.alice.com/cors"
var xhr = new XMLHttpRequest();
xhr.open("PUT",url,true);
xhr.setRequestHeader('X-Custom-Header',"value");  //发送自定义头信息X-Custom-Header
xhr.send();
```
浏览器发现这是一个非简单请求，就自动发出一个“预检”请求，要求服务器确认可以这样请求。下面是这个“预检”请求的HTTP头信息。
```http
OPTIONS /cors HTTP/1.1
Origin:http://api.bob.com
Access-Control-Request-Method:PUT
Access-Control-Request-Headers:X-Custom-Header
Host:api.slice.com
Connection:keep-alive
User-Agent:Mozilla/5.0
```
"预检"请求用的方法是OPTIONS，表示这个请求是用来询问的。头信息里面，关键字段是Origin，表示请求来自哪个源。除了Origin字段，“预检”请求的头信息包括两个特殊字段。

1.Access-Control-Request-Method：该字段是必须的。用来列出浏览器的CORS请求会用到哪些HTTP方法，上例是PUT。

2.Access-Control-Request-Headers：该字段是一个逗号分隔的字符串，指定浏览器CORS请求会额外发送的头信息字段，上例是X-Custom-Header。

#### 2.4.2预检请求的回应
服务器收到“预检”请求以后，检查了Origin，Access-Control-Request-Method，Access-Control-Request-Headers字段以后，确认允许跨源请求，就可以做出回应。
```http
HTTP/1.1 200 OK
Date:Mon,01 Dec 2008 01:15:39 GMT
Server:Apache/2.0.61(Unix)
Access-Control-Allow-Origin:http:api.bob.com
Access-Control-Allow-Methods:GET,POST,PUT
Access-Control-Allow-Headers:X-Custom-Header
Content-Type:text/htmllcharset=utf-8
Content-Encoding:gzip
Content-Length:0
Keep-Alive:timeout=2,max=100
Connection:keep-alive
Content-Type:text-plain
```
上面的HTTP回应中，关键的是Access-Control-Allow-Origin字段，表示http:api.bob.com可以请求数据。该字段也可以设为星号，表示同意任意跨源请求。Access-Control-Allow-Origin:*

如果浏览器否定了“预检”请求，会返回一个正常的HTTP回应，但是没有任何CORS相关的头信息字段。这时，浏览器就会认定，服务器不同意“预检”请求，因此触发一个错误，被XMLHttpRequest对象的onerror回调函数捕获。控制台打印出如下的报错信息。
```
XMLHttpRequest cannot load http://api.alice.com.
Origin http://api.bob.com is not allowed by Access-Control-Allow-Origin.
```
服务器回应的其他CORS相关字段如下。
```http
Access-Control-Allow-Methods:GET,POST,PUT
Access-Control-Allow-Headers:X-Custom-Header
Access-Control-Allow-Credentials:true
Access-Control-Max-Age:1728000
```
1.Access-Control-Allow-Methods:该字段必须。它的值是逗号分隔的一个字符串，表明服务器支持的所有跨域请求的方法。注意，返回的是所有支持的方法，而不单是浏览器请求的那个方法。这是为了避免多次“预检”请求。

2.Access-Control-Allow-Headers：如果浏览器请求包括Access-Control-Request-Headers字段，则Access-Control-Request-Headers字段是必须的。他也是一个逗号分隔的字符串，表明服务器支持的所有头信息字段，不限于浏览器在“预检”中请求的字段。

3.Access-Control-Allow-Credentials：该字段与简单请求时的含义相同。

4.Access-Control-Max-Age：该字段可选。用来指定本次预检请求的有效期，单位为秒。上面结果中，有效期是20天（1728000秒），即允许缓存该条回应20天，在此期间，不必发出另一条预检请求。

#### 2.4.3 浏览器的正常请求和回应
一旦服务器通过了"预检"请求,以后每次正常的CORS请求，就都跟简单请求一样，会有一个Origin头信息字段。服务器的回应，也都会有一个Access-Control-Allow-Origin头信息字段。

下面是“预检”请求之后，浏览器的正常CORS请求。
```http
PUT /cors HTTP/1.1
Origin:http://api.bob.com
Host:api.alice.com
X-Custom-Header:value
Accept-Language:en-US
Connection:keep-alive
User-Agent:Mozilla/5.0...
```
上面的头信息Origin字段是浏览器自动添加的。下面是服务器正常的回应。
```http
Access-Control-Allow-Origin:http://api.bob.com
Content-Type:text-html;charset=utf-8
```
上面头信息中，Access-Control-Allow-Origin字段是每次回应都必定包含的。

## CORS与JSONP的比较
CORS与JSONP的使用目的相同，但是CORS比JSONP更强大。

JSONP只支持GET请求，CORS支持所有类型的HTTP请求。

JSONP的优势在于支持老式浏览器，以及可以向不支持CORS的网站请求数据。


## 3.主域相同可以设置document.domain
对于主域相同而子域不同的例子，可以通过设置document.domain的办法来解决。具体的做法是可以在http://www.a.com/a.html和http://script.a.com/b.html两个文件中分别加上document.domain = ‘a.com’；然后通过a.html文件中创建一个iframe，去控制iframe的contentDocument，这样两个js文件之间就可以“交互”了。当然这种办法只能解决主域相同而二级域名不同的情况，如果把script.a.com的domian设为alibaba.com，会报错！举例如下：
```js
//www.a.com上的a.html
document.domain = 'a.com';
var ifr = document.createElement('iframe');
ifr.src = 'http://script.a.com/b.html';
ifr.style.display = 'none';
document.body.appendChild(ifr);
ifr.onload = function(){
    var doc = ifr.contentDocument || ifr.contentWindow.document;   //获取script.a.com/b.html页面，doc就是b.html的document。
    // 在这里操纵b.html
    alert(doc.getElementsByTagName("h1")[0].childNodes[0].nodeValue);
};

//script.a.com上的b.html
document.domain = 'a.com';
```
这种方式适用于{www.kuqin.com, kuqin.com, script.kuqin.com, css.kuqin.com}中的任何页面相互通信。

备注：某一页面的domain默认等于window.location.hostname。主域名是不带www的域名，例如a.com，主域名前面带前缀的通常都为二级域名或多级域名，例如www.a.com其实是二级域名。 domain只能设置为主域名，不可以在b.a.com中将domain设置为c.a.com。

### 问题：

安全性，当一个站点（b.a.com）被攻击后，另一个站点（c.a.com）会引起安全漏洞。

如果一个页面中引入多个iframe，要想能够操作所有iframe，必须都得设置相同domain。

## 4. 使用HTML5 postMessage
HTML5中最酷的新功能之一就是 跨文档消息传输 Cross Document Messaging。下一代浏览器都将支持这个功能：Chrome 2.0+、Internet Explorer 8.0+, Firefox 3.0+, Opera 9.6+, 和 Safari 4.0+ 。 Facebook已经使用了这个功能，用postMessage支持基于web的实时消息传递。

otherWindow.postMessage(message, targetOrigin);

otherWindow: 对接收信息页面的window的引用。可以是页面中iframe的contentWindow属性；window.open的返回值；通过name或下标从window.frames取到的值。

message: 所要发送的数据，string类型。

targetOrigin: 用于限制otherWindow，“*”表示不作限制

以下是演示a.com/index.html向b.com/index.html传送数据

```javascript
//a.com/index.html中的代码：
<iframe id="ifr" src="b.com/index.html"></iframe>
<script type="text/javascript">
window.onload = function() {
    var ifr = document.getElementById('ifr');
    var targetOrigin = 'http://b.com';  // 若写成'http://b.com/c/proxy.html'效果一样
                                        // 若写成'http://c.com'就不会执行postMessage了
    ifr.contentWindow.postMessage('I was there!', targetOrigin);
};
</script>

//b.com/index.html中的代码：
<script type="text/javascript">
    window.addEventListener('message', function(event){
        // 通过origin属性判断消息来源地址
        if (event.origin == 'http://a.com') {
            alert(event.data);    // 弹出"I was there!"
            alert(event.source);  // 对a.com、index.html中window对象的引用
                                  // 但由于同源策略，这里event.source不可以访问window对象
        }
    }, false);
</script>
```


## 5. 利用iframe和location.hash
这个办法比较绕，但是可以解决完全跨域情况下的脚步置换问题。原理是利用location.hash来进行传值。在url： http://a.com#helloword 中的‘#helloworld’就是location.hash，改变hash并不会导致页面刷新，所以可以利用hash值来进行数据传递，当然数据容量是有限的。

假设域名a.com下的文件cs1.html要和cnblogs.com域名下的cs2.html传递信息，cs1.html首先创建自动创建一个隐藏的iframe，iframe的src指向cnblogs.com域名下的cs2.html页面，这时的hash值可以做参数传递用。cs2.html响应请求后再将通过修改cs1.html的hash值来传递数据（由于两个页面不在同一个域下，IE、Chrome不允许修改parent.location.hash的值，所以要借助于a.com域名下的一个代理iframe；Firefox可以修改）。同时在cs1.html上加一个定时器，隔一段时间来判断location.hash的值有没有变化，有变化则获取hash值。

代码如下：
```javascript
//先是a.com下的文件cs1.html文件：
function startRequest(){
    var ifr = document.createElement('iframe');
    ifr.style.display = 'none';
    ifr.src = 'http://www.cnblogs.com/lab/cscript/cs2.html#paramdo';
    document.body.appendChild(ifr);
}
function checkHash() {
    try {
        var data = location.hash ? location.hash.substring(1) : '';
        if (console.log) {
            console.log('Now the data is '+data);
        }
    } catch(e) {};
}
setInterval(checkHash, 2000);
cnblogs.com域名下的cs2.html
//模拟一个简单的参数处理操作
switch(location.hash){
    case '#paramdo':
        callBack();
        break;
    case '#paramset':
        //do something……
        break;
}
function callBack(){
    try {
        parent.location.hash = 'somedata';
    } catch (e) {
        // ie、chrome的安全机制无法修改parent.location.hash，
        // 所以要利用一个中间的cnblogs域下的代理iframe
        var ifrproxy = document.createElement('iframe');
        ifrproxy.style.display = 'none';
        ifrproxy.src = 'http://a.com/test/cscript/cs3.html#somedata';    // 注意该文件在"a.com"域下
        document.body.appendChild(ifrproxy);
    }
}
a.com下的域名cs3.html
//因为parent.parent和自身属于同一个域，所以可以改变其location.hash的值
parent.parent.location.hash = self.location.hash.substring(1);
```

当然这样做也存在很多缺点，诸如数据直接暴露在了url中，数据容量和类型都有限等

## 6. window.name实现的跨域数据传输
window.name 传输技术，原本是 Thomas Frank 用于解决 cookie 的一些劣势（每个域名 4 x 20 Kb 的限制、数据只能是字符串、设置和获取 cookie 语法的复杂等等）而发明的（详细见原文：《Session variables without cookies》），后来 Kris Zyp 在此方法的基础上强化了 window.name 传输 ，并引入到了 Dojo （dojox.io.windowName），用来解决跨域数据传输问题。

window.name 的美妙之处：name 值在不同的页面（甚至不同域名）加载后依旧存在，并且可以支持非常长的 name 值（2MB）。

假设我们有三个页面：

a.com/app.html： 应用页面。

a.com/proxy.html： 代理文件，一般是一个没有任何内容的html文件，需要和应用页面在同一域下。

b.com/data.html： 应用页面需要获取数据的页面，可称为数据页面。

实现起来基本步骤如下：

在应用页面（a.com/app.html） 中创建一个iframe，把其src指向数据页面（b.com/data.html）。

数据页面会把数据附加到这个iframe的window.name上，data.html代码如下：
```javascript
<script type="text/javascript">
    window.name = 'I was there!';    // 这里是要传输的数据，大小一般为2M，IE和firefox下可以大至32M左右
                                     // 数据格式可以自定义，如json、字符串
</script>
```

在应用页面（a.com/app.html） 中监听iframe的onload事件，在此事件中设置这个iframe的src指向本地域的代理文件（代理文件和应用页面在同一域下，所以可以相互通信）。app.html部分代码如下：
```javascript
<script type="text/javascript">
    var state = 0, 
    iframe = document.createElement('iframe'),
    loadfn = function() {
        if (state === 1) {
            var data = iframe.contentWindow.name;    // 读取数据
            alert(data);    //弹出'I was there!'
        } else if (state === 0) {
            state = 1;
            iframe.contentWindow.location = "http://a.com/proxy.html";    // 设置的代理文件
        }  
    };
    iframe.src = 'http://b.com/data.html';
    if (iframe.attachEvent) {
        iframe.attachEvent('onload', loadfn);
    } else {
        iframe.onload  = loadfn;
    }
    document.body.appendChild(iframe);
</script>
//获取数据以后销毁这个iframe，释放内存；这也保证了安全（不被其他域frame js访问）。

<script type="text/javascript">
    iframe.contentWindow.document.write('');
    iframe.contentWindow.close();
    document.body.removeChild(iframe);
</script>
```

### 总结：
iframe的src属性由外域转向本地域，跨域数据即由iframe的window.name从外域传递到本地域。这个就巧妙地绕过了浏览器的跨域访问限制，但同时它又是安全操作。


CORS：原文来自：阮一峰老师的博客  

其中有些内容来自：[媛媛的小窝博客之跨域](http://www.zyy1217.com/2016/12/03/%E8%B7%A8%E5%9F%9F/)
