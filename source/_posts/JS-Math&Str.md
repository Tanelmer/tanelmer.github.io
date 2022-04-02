---
title: JS中math，string对象理解
date: 2016-08-15
categories: [前端,javascript]
tags: [JavaScript]
description: JS内置对象学习
---
### Math对象的一些常用方法
PI:3.1415926.....
abs(x) 返回数字的相对值
ceil(x) 返回 x 四舍五入后的最大整数
floor(x) 返回 x 四舍五入后的最小整数
max(x,y) 返回 x 和 y 之间较大的数
min(x,y) 返回 x 和 y 之间较小的数
random() 返回位于 0 到 1 之间的随机函数
<!-- more -->
## 指定范围内随机数
1产生一个0-max的随机数
// max - 期望的最大值
parseInt(Math.random()\*(max+1),10);
Math.floor(Math.random()\*(max+1));
2产生一个1-max的随机数
// max - 期望的最大值
parseInt(Math.random()\*max,10)+1;
Math.floor(Math.random()\*max)+1;
Math.ceil(Math.random()\*max);
3产生一个min-max的随机数
// max - 期望的最大值
// min - 期望的最小值 
parseInt(Math.random()\*(max-min+1)+min,10);
Math.floor(Math.random()\*(max-min+1)+min);
round(x) 四舍五进后与整
pow(x,y) 返回 y^x 的值

>Math 对象并不像 Date 和 String 那样是对象的类，因此没有构造函数 Math()，像 Math.sin() 这样的函数只是函数，不是某个对象的方法。您无需创建它，通过把 Math 作为对象使用就可以调用其所有属性和方法。

***
### String对象的一些常用方法
String
toLowerCase() 把字符串中的文本变成小写
toUpperCase() 把字符串中的文本变成大写
split(delimiter)将字符串分配为数组
substr(startIndex, length) 从startIndex与,取length个字符
substring(startIndex, endIndex) 从startIndex和endIndex之间的字符,没有包含endIndex
indexOf(searchString, startIndex) 返回字符串中第一个呈现指定字符串的地位
lastlndexOf(searchString, startIndex) 返回字符串中最后一个呈现指定字符串的地位
charAt(index) 返回指定索引处的字符
replace(regex, newString)将字符串中的某些字符替代成其它字符