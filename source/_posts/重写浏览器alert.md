---
title: 简单的JQ-alert插件
date: 2017-06-21
categories: [前端,javascript]
tags: [JavaScript]
description: 最简单的alert插件
---
### 重写window.alert
***
其实主要还是想多练手，毕竟熟能生巧，看得多写得多了自然会的就多了。
理解理解这简单的JQ插件思路，alert为例。
<!-- more -->
对于alert基本上前端汪都不会陌生了，简单的理解下封装的思路吧，虽然这个插件也非常简单，之前在职的公司在用的，大部分copy下来了。
看了一遍之前的代码，利用js和css就可以了，插件封装的思想跟JQ插件一致，用extend函数整合用户传入的参数对象，然后实现定制alert。
插件中先把弹窗dom结构写好，用append添加到body节点下，绑定按钮回调事件，根据调用参数动态的改变alert弹窗的宽高这些，非常实用。
```javascript
	window.alert = function(options){
		if(typeof(options) != 'object'){
			options = {
				con:options
			}
		}
		var opts = $.extend({
			tit:'标题',
			con:'内容',
			btn:['确定'],
			width:260,
			height:225,
			tClose: true, 
			fClose: true, 
			ok: function() {}, 
			no: function() {} 
		},options);
		var btnStr = '',
			btnCount = opts.btn.length,
			btnWidth = opts.width / btnCount;
		for(var i = 0; i < btnCount; i++) {
			if(i == 0) {
				btnStr += '<div style="display:inline-block;"><button class="alert-btn trueBtn" id="alertTrue" style="width:' + btnWidth + 'px;">' + opts.btn[i] + '</button></div >';
			} else {
				btnStr = '<div style="display:inline-block;"><button class="alert-btn falseBtn" id="alertFalse" style="width:' + btnWidth + 'px;">' + opts.btn[i] + '</button></div >' + btnStr;
			}
		}
	 	var alertStr = '' +
		'<div class="layout alert-layout" id="alert-layout">' +
		'<div class="alert" style="width:' + opts.width + 'px; height:' + opts.height + 'px;">' +
		'<div class="alert-tit">' + opts.tit + '</div>' +
		'<section class="alert-con" style="width:' + opts.width + 'px; height:' + (opts.height-65) + 'px;">' + opts.con + '</section>' +
		'<div class="alert-btn-group">' + btnStr + '</div>' +
		'</div>' +
		'</div>';
		$('body').append(alertStr);
		$('#alertTrue').click(function() {
			if(opts.tClose) {
				alertClose();
			}
			opts.ok();
		});
		$('#alertFalse').click(function() {
			if(opts.fClose) {
				alertClose();
			}
			opts.no();
		});
		function alertClose() {
			$('#alert-layout').remove();
		}
	}
	
	alert({
		tit:'练习封装alert',
		con:'点击OK跳转至blog',
		btn:['OK','NO'],
		ok:function(){
			location.href = 'https://tanelmer.github.io'
		}
	});
```