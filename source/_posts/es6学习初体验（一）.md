---
title: es6新特性初体验（一）
date: 2018-09-13
categories: [前端,javascript]
tags: [JavaScript]
description: 拥抱变化，多玩玩新东西
---
## 新特性还是要玩玩
  其实之前就有关注es6，只是工作上基本上没有机会用的到，所以就把它搁置在一旁没有再给予过多的关注了
  好了，不吹比了，还是继续老实点看书学习，先在这记录下，路还很漫长啊
<!-- more -->
***
es6最常用的新特性
`let,const,arrow funciton,template string,对象字面量,表达式结构,class等等`
1. 变量声明
    除了var以外，新加了let，const，let声明的变量只在块级作用域内有效，
而const是用来声明常量的，以后再也不用写大写字母下划线来标明这个玩意是常量了，惊不惊喜，意不意外。
2. 箭头函数
    这个应该是用的最多的一种特性了吧，据说是这样，反正我现在也没怎么开始写es6，以后再慢慢看是否属实。
    箭头函数呢，书上说跟之前coffeescript的“胖箭头”类似，箭头函数其实就是一个匿名函数，用箭头来定义。
    箭头函数有四种，单一参数单行箭头函数，多参单行箭头函数，多行箭头函数，无参箭头函数，其实说了这么多，还不如代码来的实在。
```javascript
    1. const fn = foo => `${foo} world` // return foo + world 单一参数单行箭头函数
    2. let array = ['a','aab','ac','cd'];
        array = array.filter(item => item.length >= 3) // return aab
       let array = ['a','aab','aced','cd'];
        array = array.sort((a,b) => a.length < b.length) // return aced aab cd a 多参单行箭头函数
    3. (foo + bar) = {
            retrun foo + bar 
    } // 多行箭头函数
    4. const arr = [1,2,3];
        //箭头函数
        const squares = arr.map(x => x * x);
        //传统语法
        const squares = arr.map(function (x) { return x * x });
```
箭头函数还有个神奇的this指向的改变，具体咋样之后我看完实践后再记录吧~，今天先到这了


