---
title: js面向对象
date: 2018-08-16
categories: [前端,javascript]
tags: [JavaScript]
description: js创建对象几种方式与js继承的实现
---
## ECMAScript中的创建对象模式与继承
***
>ECMAScript支持面向对象编程，但是没有类和接口。对象可以在代码执行过程中创建和增强，因此具有动态性而非严格定义的实体。在没有类的情况下，可以采用下列模式创建对象。

<!--more-->
***
### js创建对象几种方式
```javascript
	//工厂模式  解决了创建相似对象的问题
	function make(name,size,price){
		var o =  new Object();
		o.name = name;
		o.size = size;
		o.price = price;
		o.showPrice = function(){
			console.log(this.price);
		};
		return o;
	}
	var suit = make('nike','xl',666);
	
	//构造函数模式  
	function Make(name,size,price){
		this.name = name;
		this.size = size;
		this.price = price;
		this.showPrice = function(){
			console.log(this.price);
		};
	}
	var suit = new Make('nike','xl',666);
		
	//原型模式
	function Make(){
	}
	Make.prototype.name = 'nike';
	Make.prototype.size = 'xl';
	Make.prototype.price = 666;
	Make.prototype.showPrice = function(){
		console.log(this.price);
	}
	
	var suit1 = new Make();
	var suit2 = new Make();
	suit1.name = '360';
	console.log(suit1.name); //'360' —— 来自实例
	console.log(suit2.name); //'nike' —— 来自原型
	delete suit1.name;
	console.log(suit1.name); //'nike' —— 来自原型
	
	//组合模式 目前用的最多，认同度最高的一种创建自定义类型的方法
	function Make(name,size,price){
		this.name = name;
		this.size = size;
		this.price = price;
		this.number = 2;
	}
	Make.prototype = {
		constructor : Make,
		showPrice : function(){
			console.log(this.price);
		}
	}
	
	var suit1 = new Make('nike','xl',666);
	var suit2 = new Make('360','l',233);
	console.log(suit1.name === suit2.name); //false 
	console.log(suit1.showPrice === suit2.showPrice); //true 
	
```
***
### js继承的实现
> js中继承的实现原理：利用原型让一个引用类型继承另一个引用类型的属性和方法。

***
```javascript
	//同样的 原型继承会带来一些问题，第一个就是引用类型属性修改时会把对象内所有该属性修改，第二个就是创建子类函数实例时无法向超类型的构造函数传参
	//从而有个更好的继承方式  组合继承 原型+构造函数
	function SuperType(name){
		this.name = name;
		this.color = ["red","blue","green"];
	}
	SuperType.prototype.sayName = function(){
		console.log(this.name);
	}
	function SubType(name,age){
		//使用call方法继承SuperType 和  属性
		SuperType.call(this.name);
		this.age = age;
	}
	//继承方法
	SubType.prototype = new SuperType();
	SubType.prototype.constructor = SubType;
	SubType.prototype.sayAge = function(){
		console.log(this.age)
	};
```
