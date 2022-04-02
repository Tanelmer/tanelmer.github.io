---
title: Vue源码解析-Flow&目录设计
date: 2018-09-03
categories: 前端
tags: [JavaScript,Vue]
description: 深入了解Vue框架，源码设计
---

## Flow-js静态类型检查工具 [官网](https://flowtype.org/)
**Flow是什么？**  
[Flow](https://flowtype.org/)是个JavaScript静态类型检查工具。  
<!-- more -->

**Flow有什么用？为什么要用？**  
Flow是用于检测编译前出现的类型错误，为什么需要检测？这是由于js是弱数据类型的语言，弱(动态)数据类型代表在代码中，变量或常量会自动依照赋值变更数据类型，而且类型种类也很少，这是直译式脚本语言的常见特性，但有可能是优点也是很大的缺点。优点是容易学习与使用，缺点是像开发者经常会因为赋值或传值的类型错误，造成不如预期的结果。有些时候在使用框架或函数库时，如果没有仔细看文件，亦或是文件写得不清不楚，也容易造成误用的情况。

这个缺点在应用规模化时，会显得更加严重。我们在开发团队的协同时，一般都是用详尽的文字说明，来降低这个问题的发生，但JS语言本身无法有效阻止这些问题。而且说明文件也需要花时间额外编写，其他的开发者阅读也需要花时间。在现今预先编译器流行的年代，像TypeScript这样的强(静态)类的JavaScript超集语言就开始流行，用严格的角度，以JavaScript语言为基底，来重新打造另一套具有强(静态)类型特性的语言，就如同Java或C#这些语言一样，这也是为什么TypeScript称自己是企业级的开发JavaScript解决方案。

> 注: 强(静态)类型语言，意思是可以让变量或常量在声明(定义)时，就限制好只能使用哪种类型，之后在使用时如果发生类型不相符时，就会发出错误警告而不能编译。但不只这些，语言本身也会拓展了更多的类型与语法。  

**如何使用Flow**  
```javascript
//参数类型未知
function foo(x){
    return x+1
}

// @flow
function foo(x: number): number {
  return x + 10
}
foo('hi')
/* 你有看到在函数的传参，以及函数的圆括号(())后面的两个地方，加了: number标记，这代表这个传参会限定为数字类型，而返回值也只允许是数字类型。
当使用非数字类型的值作为传入值时，就会出现由Flow工具发出的警告消息，像下面这样:
> message: '[flow] string (This type is incompatible with number See also: function call)'*/

// 允许多种类型参数
// @flow

function foo(x: number | boolean): number | string {
  if (typeof x === 'number') {
    return x + 10
  }
  return 'x is boolean'
}

foo(1)
foo(true)
foo(null)  // 这一行有类型错误消息
```
对于Flow的介绍大概就这些，想要详细了解Flow的安装使用，请阅读[官网文档](https://flowtype.org/)。

## Vue源码目录设计

vue源码都在src目录下，目录结构为：  
```javascript
src  
|—— compiler.js     //构建相关代码-会把模板解析成ast语法树等等功能模块  
|—— core.js         //包含Vue核心代码，内置组件，全局API封装，Vue实例化，观察者，虚拟Dom等  
|—— platform.js     //区分Vue不同的版本，兼容多平台  
|—— server.js       //服务端渲染相关功能  
|—— sfc.js          //解析.vue文件成y一个js对象
|—— shared.js       //一些与浏览器共存的工具函数
```

