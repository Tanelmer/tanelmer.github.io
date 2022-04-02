---
title: Typescript-实践初体验（一）
date: 2021-01-03
categories: Typescript
tags: [Typescript,javascript,前端]
description: Typescript 学习记录（-）
---

<a name="a7OwN"></a>
### 基础类型
对比js的数据类型，多了元组【tuple】、枚举【enum】、any、void、null、undefined、never、object。

<!--more-->

<a name="AThS7"></a>
#### tuple【元组】
元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。 比如，你可以定义一对值分别为 string和number类型的元组。

```javascript
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ['hello', 10]; // OK
// Initialize it incorrectly
x = [10, 'hello']; // Error

console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'

x[3] = 'world'; // OK, 字符串可以赋值给(string | number)类型
console.log(x[5].toString()); // OK, 'string' 和 'number' 都有 toString
x[6] = true; // Error, 布尔不是(string | number)类型
```

<a name="DPCt6"></a>
#### enum【枚举】
enum类型是对JavaScript标准数据类型的一个补充。 像C#等其它语言一样，使用枚举类型可以为一组数值赋予友好的名字。

```javascript
enum Color {Red, Green, Blue}
let c: Color = Color.Green;

// 指定值
enum Color {Red = 1, Green, Blue}
let c: Color = Color.Green;

enum Color {Red = 1, Green = 2, Blue = 4}
let c: Color = Color.Green;

// 由value得到key
enum Color {Red = 1, Green, Blue}
let colorName: string = Color[2];

console.log(colorName);  // 显示'Green'因为上面代码里它的值是2
```

<a name="3p7h5"></a>
#### any【任意型】
任意类型，当我们不确定变量类型时，比如用户输入或者第三方库的变量，这种情况下，我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。

```javascript
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean

// 部分数据类型已知
let list: any[] = [1, true, "free"];
list[1] = 100;
```

<a name="yu2gV"></a>
#### void【无返回】
无返回值，一般是没啥实际用处的，这个类型的变量只能被赋值undefined、null。

```javascript
function warnUser(): void {
    console.log("This is my warning message");
}
```

null、undefined：默认情况下null和undefined是所有类型的子类型。 就是说你可以把 null和undefined赋值给number类型的变量。然而，当你指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自。 这能避免 很多常见的问题。 也许在某处你想传入一个 string或null或undefined，你可以使用联合类型string | null | undefined。

<a name="yYeHI"></a>
#### never【不存在】
表示的是那些永不存在的值的类型。目前我不知道有啥用。。

<a name="mmHRI"></a>
#### object【非原始】
表示非原始类型，也就是除number，string，boolean，symbol，null或undefined之外的类型。

```javascript
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error
```

<a name="gOZyM"></a>
### 类型断言
> 类型断言：类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 TypeScript会假设你，程序员，已经进行了必须的检查。


转述成白话文就是，我知道我在干嘛，不用你检测了，相当于白名单的感觉。（个人理解）。
<a name="e5HCJ"></a>
#### 断言有两种形式
1、尖括号，as语法。<br />2、建议采用as，适用于jsx语法。

```javascript
// 尖括号
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;

// as
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```

