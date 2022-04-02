---
title: JQuery-rolling无缝向上滚动
date: 2016-07-09
categories: 前端
tags: [JavaScript，JQuery]
description: 简单的jQuery无缝向上滚动效果演示
---
### 对于JQuery-rolling插件的思考
确实~ 我是个菜鸟，这点不用多说~ 大神请绕道，不然就班门弄斧了~~

<!--more-->
*应用场景：*一般用于信息展示窗口 向上无缝滚动  > [演示地址](http://www.jq22.com/yanshi6631)

*大致实现思路：*用animate往上滚（marginTop） 1px，需要注意的是动画回调里写的玩意，这是实现无缝的精华,然后就是跑定时器，为啥要用遍历去包裹定时器我还没弄懂，我去掉遍历demo跑起来也没问题，然后看遍历影响的是intId这个定时器对象数组，估计是出于性能方面的考究，清除定时器只清除对应的？还不是特别懂，希望有懂的朋友告知，然后继续学习吧~

*感悟：*之前在公司也有写过这种业务需求的小插件，思路大体类似，用的scrollTop这个属性，这玩意不好的地方就是会产生滚动条，无缝实现起来没那么容易，而且animate动画不支持，然后就不流畅~，滚一行是一行，滚到底了又重新回到顶上继续滚，然后突然觉得**对于一个问题，想出解决办法可能不是难事，想出最佳的解决办法才是比较难的~**，所以关于思想，算法的考虑就尤为重要了吧~算法=。=~~
***
JavaScript：
```javascript
// JavaScript Document
	(function($){
		$.fn.myScroll = function(options){
		//默认配置
		var defaults = {
			speed:40,  //滚动速度,值越大速度越慢
			rowHeight:24 //每行的高度
		};
		
		var opts = $.extend({}, defaults, options),intId = [];
		
		function marquee(obj, step){
		
			obj.find("ul").animate({
				marginTop: '-=1'
			},0,function(){
					var s = Math.abs(parseInt($(this).css("margin-top")));
					if(s >= step){
						$(this).find("li").slice(0, 1).appendTo($(this));
						$(this).css("margin-top", 0);
					}
				});
			}
			
			this.each(function(i){
				var sh = opts["rowHeight"],speed = opts["speed"],_this = $(this);
				intId[i] = setInterval(function(){
					if(_this.find("ul").height()<=_this.height()){
						clearInterval(intId[i]);
					}else{
						marquee(_this, sh);
					}
				}, speed);
	
				_this.hover(function(){
					clearInterval(intId[i]);
				},function(){
					intId[i] = setInterval(function(){
						if(_this.find("ul").height()<=_this.height()){
							clearInterval(intId[i]);
						}else{
							marquee(_this, sh);
						}
					}, speed);
				});
			
			});
	
		}
	
	})(jQuery);
```
