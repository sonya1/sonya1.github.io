---
layout: post
title: 跨域
category: workFighting
tags: 跨域
keywords: 跨域
description: 
---

### 什么是跨域
| URL | 说明 | 是否允许通信 | 
| - | :-: | -: | 
| http://www.a.com/a.js，http://www.a.com/b.js | 同一域名下| 允许 | 
| http://www.a.com/lab/a.js，http://www.a.com/scripts/b.js | 同一域名下不同文件夹| 允许 | 
| http://www.a.com：8000/a.js，http://www.a.com/b.js | 同一域名，不同端口| 不允许（默认端口80,8080） | 
| http://www.a.com/a.js，https://www.a.com/b.js | 同一域名，不同协议| 不允许 | 
| http://www.a.com/a.js，http://70.32.92.74/b.js | 域名和域名对应IP| 不允许 | 
| http://www.a.com/a.js，http://script.a.com/b.js | 主域相同，子域不同| 不允许 | 
| http://www.a.com/a.js，http://a.com/b.js | 同一域名,不同二级域名| 不允许（cookie这种情况下也不允许访问） | 
| http://www.cnblogs.com/a.js，http://a.com/b.js | 不同域名| 不允许 | 

协议，域名，端口必须一样才可以！

### JSONP：借助<script>发送HTTP请求
  <script>元素可以作为一种AJAX传输机制：只须设置<script>元素的src属性（假如他还没插入到document中，需要插入进去），然后浏览器就会发送一个HTTP请求以下载src属性所指向的URL。使用<script>元素进行Ajax传输的一个主要原因是,它不受同源策略的影响，因此可以使用它从其他的服务器请求数据，第二个原因是包含JSON编码数据的响应体会自动解码（执行）。  
    
  这种使用<script>元素作为Ajax传输的技术叫JSONP，若HTTP请求所得到的响应数据是经过JSON编码的，则适合使用该技术。P代表“填充”或“前缀”。  
  
  假设你已经写了一个服务，他处理GET请求并返回JSON编码的数据。同源的文档可以在代码中使用XMLHttpRequest和JSON.parse()。假设在服务器上启用了CORS，在新的浏览器下，跨域的文档也可以使用XMLHttpRequest享受到该服务。在不支持CORS的旧的浏览器下，跨域文档只能通过<script>元素访问这个服务。使用JSONP，JSON响应数据（理论上）适合放的JS代码，当他到达浏览器将执行他。相反，不使用jSONP,而是对JSON编码过的数据解码，结果还是数据，并没有做任何事情。  
  
  这就是JSONP中P的意义所在。当通过<script>元素调用数据时，响应内容必须用JavaScript函数名和圆括号包裹起来。而不是发送这样一段JSON数据：[1,2,{"name":"sonya"}]。他会发送这样一个包裹后的JSON响应：
  ```
  handleResponse(
    [1,2,{"name":"sonya"}]
  )
  ```
  
  包裹后的响应会成为<script>元素的内容，他先判断JSON编码后的数据（毕竟就是一个javascript表达式），然后把他传给handleResponse()函数，我们可以假设，文档会拿这些数据做一些有用的事情。  
  
  为了可行起见，我们必须通过某种方式告诉服务，它正在从一个<script>元素调用，必须返回一个JSONP响应，而不应该是普通的JSON响应。这个可以通过在URL中添加一个查询参数来实现：例如，追加“?json”(或&json)。  
  
  在实践中，支持JSONP的服务不会强制指定客户端必须实现的回调函数名称，比如handleResponse。相反，它们使用查询参数的值，允许客户端指定一个函数名，然后使用函数名去填充响应。（见下例）许多支持JSONP的服务都能分辨出这个参数名。另一个常见的参数名称是callback，为了让使用到的服务支持类似特殊的需求，就需要在代码上做一些修改了。  
  ```
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
  创建一个新的<script>元素，设置其URL，并把它插入到文档中，正是该插入操作触发HTTP请求。
  ————《JS权威指南》
  -------------------------------------------------------------------------------------------------
  核心原理：html的script标签可以加载并执行其他域的JS文件。  
  JSONP原理：  
  1.首先在客户端注册一个callback函数，然后把callback的名字传给服务器。  
  2.服务器先生成json数据，然后以js语法的方式，生成一个function，function的名字就是客户端传过来的callback参数值，最后将json数据直接以入参的方式，放置到function中，这样就生成了一段js脚本，返回给客户端。  
  3.客户端解析script标签，并执行返回的js脚本，此时数据作为参数，传入到了客户端预先定义的callback函数里（动态执行回调函数）。  
  
  举个例子：
  ```
  //客户端
  <script>
    function doSomething(jsonData){
      //处理获得的json数据
    }
  </script>
  <script src="http://example.com/data.php?callback=doSomething"></script>
  //服务器端 data.php
  <?php
    $callback = $_GET['callback'];
    $data = array["a","b","c"];
    echo $callback."(".$data.")";
  ?>
  ```
