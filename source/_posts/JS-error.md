---
title: JS异常处理
date: 2016-07-18
categories: [前端,javascript]
tags: [JavaScript]
description: js中异常机制
---

## JS异常处理机制
ECMA-262第三版引入了跟java中一样的try catch语句，用于js代码中的错误处理。
<!-- more -->
```javascript
<script>
	var tip = "正常执行",err = "异常处理1";
	function try_catch(){
		try{
			//可能会导致错误的代码
			console.log(tips);
		}catch(error){
			//在错误时怎么处理
			console.log(err);
			console.log(error);
			console.log(error.message);
			throw '任何玩意都行，反正是自定义的错误信息'; //抛出异常
			return ;
		}finally{
			console.log('最后始终要执行。'); //return都毫无效果
		}
	}
	try_catch();
</script>
```
浏览器 console结果：

异常处理 
exception.html:25 ReferenceError: tips is not defined
    at try_catch (exception.html:22)
    at exception.html:33
exception.html:26 tips is not defined
exception.html:30 最后始终要执行。
exception.html:27 Uncaught 任何玩意都行，反正是自定义的错误信息
try_catch @ exception.html:27
(anonymous) @ exception.html:33

*补充：try catch只适用于同步代码，异步异常无法捕获*
## 需要注意的地方：finally是不管try catch怎样都会执行的，所以用的时候要慎重。

