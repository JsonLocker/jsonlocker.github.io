---
title: 手机防止滑动触发点击
date: 2019-10-22 11:58:51
categories: 
	- Javascript
tags:
	- javascript
---

CODE


```javascript

function stopTouchendPropagationAfterScroll(){
	var locked = false;

	window.addEventListener('touchmove', function(ev){
		locked || (locked = true, window.addEventListener('touchend', stopTouchendPropagation, true));
	}, true);

	function stopTouchendPropagation(ev){
		ev.stopPropagation();
		window.removeEventListener('touchend', stopTouchendPropagation, true);
		locked = false;
	}
}

```
