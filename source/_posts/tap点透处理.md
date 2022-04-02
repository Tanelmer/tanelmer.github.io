---
title: 移动前端tap事件点透解决方案
date: 2017-11-09
categories: 前端
tags: [JavaScript,前端]
description: tap点透解决
---
### 移动前端tap事件问题解决或替代方案
tap轻触事件，对于做移动前端的同学们大多来说都不陌生了，但zepto在封装tap的时候有点小瑕疵，以至于产生了tap点透，那什么是tap点透呢？
<!-- more -->
***
## 问题描述：
tap事件的出现是为了解决移动click延迟300ms响应问题，而点透问题的产生是因为zepto把touch事件绑定在document上，所以冒泡就等于阻止不了，因为document已经在最上层了，所以tap事件在触发时有可能触发到非当前层级，以至出现点透。
## 解决：
1. 使用替代方案，github有个fastclick.js，这个库能替代click事件，解决了click延迟响应。
> 因为他事件是绑定到document上，先touchstart然后touchend，根据touchstart的event参数判断该dom是否注册了tap事件，有就触发，于是问题来了，zepto的touchend这里有个event参数，我们event.preventDefault()，这里本来都是最上层了，这就代码压根没什么用，但是fastclick处理办法不可谓不巧妙，这个库直接在touchend的时候就触发了dom上的click事件而替换了本来的触发时间，意思是原来要350-400ms执行的代码突然就移到了50-100ms，然后这里虽然使用了touch事件但是touch事件是绑定到了具体dom而不是document上，所以e.preventDefault是有效的，我们可以阻止冒泡，也可以阻止浏览器默认事件，这个才是fastclick的精华部分，不可谓不高啊！！！
github地址:[https://github.com/ftlabs/fastclick](https://github.com/ftlabs/fastclick)

2. touchend事件替代tap事件
因为tap是绑在document上，所以才会点透，touchend可绑在dom上，用even.preventDefault()可以阻止冒泡，防止点透。
