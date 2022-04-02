---
title: JS中的Event-loop
date: 2020-03-01
categories: [前端,javascript]
tags: [javascript,前端]
description: JavaScript-JS中的Event-loop
---

<a name="e8FK1"></a>
### 一道面试题
理解Event-loop之前，我们先来看一道经典的面试题。
<!--more-->

> 最后的打印顺序是什么？
> ```javascript
console.log(1);

setTimeout(() => {
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3)
  });
});

new Promise((resolve, reject) => {
  console.log(4)
  resolve(5)
}).then((data) => {
  console.log(data);
})

setTimeout(() => {
  console.log(6);
})

console.log(7);
```

是不是看着一下子有点懵，别急，relax，我们慢慢来剖析。

---

<a name="KXDDw"></a>
### 理解一些概念
**宏队列，macrotask，也叫tasks。** 一些异步任务的回调会依次进入macro task queue，等待后续被调用，这些异步任务包括：

- setTimeout
- setInterval
- setImmediate (Node独有)
- requestAnimationFrame (浏览器独有)
- I/O
- UI rendering (浏览器独有）

**微队列，microtask，也叫jobs。** 另一些异步任务的回调会依次进入micro task queue，等待后续被调用，这些异步任务包括：

- process.nextTick (Node独有)
- Promise
- Object.observe
- MutationObserver

（注：只针对浏览器和NodeJS）

<a name="UyWFt"></a>
### 看懂一张图
浏览器event-loop机制

![event-loop.png](https://picture-1256757196.cos.ap-chengdu.myqcloud.com/event.png)

解释下这个流程，发生的一些事情：

1. 执行全局Script同步代码，这些同步代码有一些是同步语句，有一些是异步语句（比如setTimeout等）；
1. 全局Script代码执行完毕后，调用栈Stack会清空；
1. 从微队列microtask queue中取出位于队首的回调任务，放入调用栈Stack中执行，执行完后microtask queue长度减1；
1. 继续取出位于队首的任务，放入调用栈Stack中执行，以此类推，直到直到把microtask queue中的所有任务都执行完毕。**注意，如果在执行microtask的过程中，又产生了microtask，那么会加入到队列的末尾，也会在这个周期被调用执行**；
1. microtask queue中的所有任务都执行完毕，此时microtask queue为空队列，调用栈Stack也为空；
1. 取出宏队列macrotask queue中位于队首的任务，放入Stack中执行；
1. 执行完毕后，调用栈Stack为空；
1. 重复第3-7个步骤；
1. 重复第3-7个步骤；

...

比较重要的3个流程：

1. 宏队列macrotask一次只从队列中取一个任务执行，执行完后就去执行微任务队列中的任务；
1. 微任务队列中所有的任务都会被依次取出来执行，知道microtask queue为空；
1. 图中没有画UI rendering的节点，因为这个是由浏览器自行判断决定的，但是只要执行UI rendering，它的节点是在执行完所有的microtask之后，下一个macrotask之前，紧跟着执行UI render。

现在可以去尝试着剖析那道题了。

<a name="y52qe"></a>
### 解题

```javascript
console.log(1);

setTimeout(() => {
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3)
  });
});

new Promise((resolve, reject) => {
  console.log(4)
  resolve(5)
}).then((data) => {
  console.log(data);
})

setTimeout(() => {
  console.log(6);
})

console.log(7);

// step1. 执行全局代码，打印1
// step2. setTimeout 放入宏任务队列，待执行
// step3. promise 同步代码先执行，打印4，.then() promise异步代码放入微任务队列
// step4. setTimeout 放入宏任务队列，待执行
// step5. 执行全局代码，打印7
// step6. 执行微任务队列，队首任务，打印5
// step7. 执行宏任务队列，队首任务，打印2，执行完后执行微任务队列，队首任务，打印3
// step8. 执行宏任务队列，队首任务，打印6

// 最后的答案就是：
// 1、4、7、5、2、3、6
// 不相信的话可以去浏览器console试试，打印结果。
```

---

<a name="DotTL"></a>
### 发现一张动图，生动形象

```javascript
const foo = () => console.log("First");
const bar = () => setTimeout(() => console.log("Second"), 500);
const baz = () => console.log("Third");

bar();
foo();
baz();

```


[https://user-gold-cdn.xitu.io/2019/12/31/16f593b2275fec67?imageslim](https://user-gold-cdn.xitu.io/2019/12/31/16f593b2275fec67?imageslim)
