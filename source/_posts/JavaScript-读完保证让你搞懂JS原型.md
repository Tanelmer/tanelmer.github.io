---
title: 读完保证让你搞懂JS原型
date: 2019-08-05
categories: [前端,javascript]
tags: [javascript,前端]
description: JavaScript-读完保证让你搞懂JS原型
---

<a name="QUcJR"></a>
### 一道面试题
**问：**__proto__是什么？prototype又是什么？它两的区别？

<!--more-->

<a name="Lrogh"></a>
### 理解一些概念
实例(构造函数实例)，构造函数，原型对象，原型链？

实例对象是通过构造函数用**new**操作符创建的，构造函数又会有一个原型prototype对象，实例内部有个原型属性__proto__，通过它实例可以访问到构造函数原型prototype对象上方法和属性。

js里所有的对象都有__proto__属性(对象，函数)，指向构造该对象的构造函数的原型。

> JavaScript 常被描述为一种**基于原型的语言 (prototype-based language)**——每个对象拥有一个**原型对象**，对象以其原型为模板、从原型继承方法和属性。原型对象也可能拥有原型，并从中继承方法和属性，一层一层、以此类推。这种关系常被称为**原型链 (prototype chain)**，它解释了为何一个对象会拥有定义在其他对象中的属性和方法。
> 
> 准确地说，这些属性和方法定义在Object的构造器函数(constructor functions)之上的`prototype`属性上，而非对象实例本身。
> 
> 在传统的 OOP 中，首先定义“类”，此后创建对象实例时，类中定义的所有属性和方法都被复制到实例中。在 JavaScript 中并不如此复制——而是在对象实例和它的构造器之间建立一个链接（它是__proto__属性，是从构造函数的`prototype`属性派生的），之后通过上溯原型链，在构造器中找到这些属性和方法。
> 
> -[MDN](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Object_prototypes)


<a name="YC1Zr"></a>
### 看懂一张图
![image.png](https://picture-1256757196.cos.ap-chengdu.myqcloud.com/%E5%8E%9F%E5%9E%8B%E9%93%BE.png)

有了上面一些概念的解释，结合着这一张图，有些基础的童鞋都已经明白的差不多了。但其实还是比较抽象，我们继续用代码实践下。

<a name="vuieD"></a>
### 解题

```javascript
// 问：__proto__是什么？prototype又是什么？它两的区别？

// 定义一个构造函数
function Car() {
  this.type = 'suv';
  this.brand = 'BMW';
}

// 通过构造函数原型添加方法
Car.prototype.drive = function() {
  console.log('开始开车了，滴滴滴');
}

// 创建一个实例对象
let myCar = new Car();

// 让我们看下这里面的大千世界
console.log('实例', myCar);
console.log('实例原型属性', myCar.__proto__);
console.log('构造函数原型', Car.prototype);
console.log(myCar.__proto__ === Car.prototype); // true

```

![image.png](https://picture-1256757196.cos.ap-chengdu.myqcloud.com/%E5%8E%9F%E5%9E%8B%E5%AE%9E%E8%B7%B5%E7%BB%93%E6%9E%9C.png)

<a name="e5lIZ"></a>
### 参考
[原型](https://bonsaiden.github.io/JavaScript-Garden/zh/#object.prototype)<br />[对象原型 - MDN](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Object_prototypes)
