---
title: Typescript-实践初体验（二）
date: 2021-11-03
categories: Typescript
tags: [Typescript, javascript, 前端]
description: Typescript 学习记录（二）
---

<a name="a7OwN"></a>

### 联合类型

联合类型（Union Types）表示取值可以为多种类型中的一种。

<!--more-->

联合类型使用 | 分隔每个类型。

```javascript
let myNum: string | number;
myNum = 'seven';
myNum = 7;
```

```javascript
let myNum: string | number;
myNum = true;

// index.ts(2,1): error TS2322: Type 'boolean' is not assignable to type 'string | number'.
//   Type 'boolean' is not assignable to type 'number'.
```

### 对象的类型——接口

在面向对象语言中，接口（Interfaces）是一个很重要的概念，它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implement）。

TypeScript 中的接口是一个非常灵活的概念，除了可用于对类的一部分行为进行抽象以外，也常用于对「对象的形状（Shape）」进行描述。

#### 可选属性

```javascript
interface Person {
  name: string;
  age?: number; // 可选
}
```

#### 任意属性
使用 [propName: string] 定义了任意属性取 string 类型的值
```javascript
interface Person {
  name: string;
  age?: number;
  [propName: string]: any;
}
```
一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集：
```javascript
interface Person {
    name: string;
    age?: number;
    [propName: string]: string; // 其余属性必须是string
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};

// index.ts(3,5): error TS2411: Property 'age' of type 'number' is not assignable to string index type 'string'.
// index.ts(7,5): error TS2322: Type '{ [x: string]: string | number; name: string; age: number; gender: string; }' is not assignable to type 'Person'.
//   Index signatures are incompatible.
//     Type 'string | number' is not assignable to type 'string'.
//       Type 'number' is not assignable to type 'string'.
```

更多接口信息可参考 [https://juejin.cn/post/6855449252785717256](https://juejin.cn/post/6855449252785717256)

### 声明文件
当使用第三方库时，我们需要引用它的声明文件，才能获得对应的代码补全、接口提示等功能。

#### 声明类型
* declare var 声明全局变量
* declare function 声明全局方法
* declare class 声明全局类
* declare enum 声明全局枚举类型
* declare namespace 声明（含有子属性的）全局对象
* interface 和 type 声明全局类型
* export 导出变量
* export namespace*  导出（含有子属性的）对象
* export default ES6 默认导出
* export = commonjs 导出模块
* export as namespace UMD 库声明全局变量
* declare global 扩展全局变量
* declare module 扩展模块
* /// < reference /> 三斜线指令