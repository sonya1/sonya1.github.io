---
layout: post
title: H5——video代码示例
category: HTML
tags: HTML5
keywords: video
description: video
---

![图1](https://sonya1.github.io/assets/img/work_fight/video.png)
```html
<!-- src中放上本地的ogv的音频 -->
<video id="v1" src="Intermission-Walk-in.ogv"></video>
<div id="div1">
	<input type="button" value="播放" />
	<input type="button" value="00:00:00" />
	<input type="button" value="00:00:00" />
	<input type="button" value="静音" />
	<input type="button" value="全屏" />
</div>
<div id="div2">
	<div id="div3"></div>
</div>

<div id="div4">
	<div id="div5"></div>
</div>
```

```css
#div2{ width:300px; height:30px; background:#666666; position:relative;}
#div3{ width:30px; height:30px; background:red; position:absolute; left:0; top:0;}
#div4{ width:300px; height:20px; background:#666666; position:relative; top:10px;}
#div5{ width:20px; height:20px; background:yellow; position:absolute; right:0; top:0;}
```

```javascript
window.onload = function(){
	var oV = document.getElementById('v1');
	var oDiv = document.getElementById('div1');
	var aInput = oDiv.getElementsByTagName('input');
	
	var oDiv2 = document.getElementById('div2');
	var oDiv3 = document.getElementById('div3');
	var oDiv4 = document.getElementById('div4');
	var oDiv5 = document.getElementById('div5');
	
	var timer = null;
	
	//播放暂停
	aInput[0].onclick = function(){
	
		if(oV.paused){
			this.value = '暂停';
			oV.play();
			toShow();
			timer = setInterval(toShow,1000);
		}
		else{
			this.value = '播放';
			oV.pause();
			clearInterval(timer);
		}
		
	};
	
	aInput[2].value = timeChange(oV.duration);
	
	function timeChange(iAll){
	
		iAll = Math.floor(iAll);
	
		var hours = toZero(parseInt(iAll/3600));
		var mintus = toZero(parseInt(iAll%3600/60));
		var sends = toZero(parseInt(iAll%60));
		
		return hours + ":" + mintus + ":" + sends;
	
	}
	
	function toZero(num){
		if(num<10){
			return '0' + num;
		}
		else{
			return '' + num;
		}
	}
	
	function toShow(){
		aInput[1].value = timeChange(oV.currentTime);
		
		var scale = oV.currentTime/oV.duration;
		
		oDiv3.style.left = scale * (oDiv2.offsetWidth - oDiv3.offsetWidth) + 'px';
		
	}
	
	//静音
	aInput[3].onclick = function(){
		if(oV.muted){
			this.value = '静音';
			oV.muted = false;
			oDiv5.style.left = oV.volume*(oDiv4.offsetWidth - oDiv5.offsetWidth) + 'px';
		}
		else{
			this.value = '消除静音';
			oV.muted = true;
			oDiv5.style.left = 0;
		}
	};
	
	var disX = 0;
	
	//进度调整
	oDiv3.onmousedown = function(ev){
		var ev = ev || window.event;
		disX = ev.clientX - oDiv3.offsetLeft;
		
		document.onmousemove = function(ev){
			var ev = ev || window.event;
			
			var L = ev.clientX - disX;
			
			if(L<0){
				L = 0;
			}
			else if(L>oDiv2.offsetWidth - oDiv3.offsetWidth){
				L = oDiv2.offsetWidth - oDiv3.offsetWidth;
			}
			
			oDiv3.style.left = L + 'px';
			
			var scale = L/(oDiv2.offsetWidth - oDiv3.offsetWidth);
			
			oV.currentTime = scale * oV.duration;
			
			toShow();
			
		};
		document.onmouseup = function(){

			aInput[0].value = '暂停';
			oV.play();
			toShow();
			timer = setInterval(toShow,1000);

			document.onmousemove = null;
			document.onmouseup = null;
		};
		return false;
	};
	
	var disX2 = 0;
	
	//声音
	oDiv5.onmousedown = function(ev){
		var ev = ev || window.event;
		disX2 = ev.clientX - oDiv5.offsetLeft;
		
		document.onmousemove = function(ev){
			var ev = ev || window.event;
			
			var L = ev.clientX - disX2;
			
			if(L<0){
				L = 0;
			}
			else if(L>oDiv4.offsetWidth - oDiv5.offsetWidth){
				L = oDiv4.offsetWidth - oDiv5.offsetWidth;
			}
			
			oDiv5.style.left = L + 'px';
			
			var scale = L/(oDiv4.offsetWidth - oDiv5.offsetWidth);	
			
			oV.volume = scale;
			
		};
		document.onmouseup = function(){
			document.onmousemove = null;
			document.onmouseup = null;
		};
		return false;
	};
	
	//全屏
	aInput[4].onclick = function(){
	
		oV.webkitRequestFullScreen();
	
	};
	
};
```
代码参考：[梦龙小站——视频实例](https://blog.csdn.net/lee_magnum/article/details/17922487)

代码参考：[梦龙小站——音频实例](https://blog.csdn.net/lee_magnum/article/details/17921743)

