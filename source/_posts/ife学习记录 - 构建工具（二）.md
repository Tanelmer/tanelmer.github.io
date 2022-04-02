---
title: ife学习记录 - 构建工具（二）
date: 2016-07-11
categories: 构建工具
tags: [构建工具]
description: 前端工程化，工具少不了，grunt、gulp、webpack、新出的parcel，一个接一个，先熟悉熟悉parcel和webpack。
---
# 构建工具（webpack）

## webpack - 时下最热门的模块打包器
webpack是一个前端资源加载/打包工具。它将根据模块的依赖关系进行静态分析，然后将这些模块按照指定的规则生成对应的静态资源。  
<!--more-->
webpack很早就出来了，之前也有用过，但是也仅仅限于简单应用，后面用vur-cli脚手架的时候觉得配置还是挺多的，然后就找了些关于vue-cli里webpack配置的资料了解下，也做了点笔记。  
[webpack-vue-cli配置详解](https://elmerlxy.com/2018/04/29/webpack-vue-cli%E9%85%8D%E7%BD%AE%E8%AF%A6%E8%A7%A3/)
>## vue-cli 命令行工具
为单页面应用快速搭建 (SPA) 繁杂的脚手架。它为现代前端工作流提供了 batteries-included 的构建设置。只需要几分钟的时间就可以运行起来并带有热重载、保存时 lint 校验，以及生产环境可用的构建版本。

## webpack文档阅读&实践
文档嘛就不用说了，当你想要了解一个之前未了解过的技术时，读文档可能是入门最快的方式，而后加上一些实践，基本上对这玩意就会有一个大概的了解。  
[webpack中文文档](https://webpack.docschina.org/concepts/)  
### webpack有几个核心概念
* 入口（entry）
* 输出（output）
* 加载器（loader）
* 插件（plugins）  
---

### 入口：打包的起始文件。
这里有两种区分，一种是单页面文件入口，还有一种是多页面文件入口。  
语法也有区分，单页和多页传参是不一样的，对应对象和数组。
webpack.config.js
```javascript
//单页面入口
module.exports = {
  entry: {
    app: './src/app.js',
    vendors: './src/vendors.js'
  }
};
//多页面入口
module.exports = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
};
```
### 输出配置也搭配的入口规则来的
```javascript
//单页输出
module.exports = {
  output: {
    filename: 'bundle.js',
    path: '/home/proj/public/assets'
  }
};
//多页输出
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
};

// 写入到硬盘：./dist/app.js, ./dist/search.js
```
有个需要注意的地方就是，你可能有需求将输出文件路径配置到cnd上，并在文件末尾加上hash来去除缓存，这里有个publicPath的配置项，可以满足你的需求。  
```javascript
module.exports = {
  //...
  output: {
    path: '/home/proj/cdn/assets/[hash]',
    publicPath: 'http://cdn.example.com/assets/[hash]/'
  }
};
```
关于path和publicPath的区别。这篇webpack解惑讲的很全很清晰。  
[文章链接](https://www.jianshu.com/p/dcb28b582318)
>“path”仅仅告诉Webpack结果存储在哪里，然而“publicPath”项则被许多Webpack的插件用于在生产模式下更新内嵌到css、html文件里的url值。
例如，在localhost（译者注：即本地开发模式）里的css文件中边你可能用“./test.png”这样的url来加载图片，但是在生产模式下“test.png”文件可能会定位到CDN上并且你的Node.js服务器可能是运行在HeroKu上边的。这就意味着在生产环境你必须手动更新所有文件里的url为CDN的路径。
然而你也可以使用Webpack的“publicPath”选项和一些插件来在生产模式下编译输出文件时自动更新这些url。  

### 加载器(loader) VS 插件(plugin)
文档解释  
>loader 类似于其他构建工具中“任务(task)”，并提供了处理前端构建步骤的强大方法。loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL。loader 甚至允许你直接在 JavaScript 模块中 import CSS文件！  
之前我总感觉loader跟plugin是一个玩意，只不过是语法引用方面有些不同，都是webpack的一些扩展，后面才发现这其中有些复杂的关系。。。  

功能思想上的区别：
>loader 用于加载某些资源文件。 因为webpack 本身只能打包commonjs规范的js文件，对于其他资源例如 css，图片，或者其他的语法集，比如 jsx， coffee，是没有办法加载的。 这就需要对应的loader将资源转化，加载进来。从字面意思也能看出，loader是用于加载的，它作用于一个个文件上。  
plugin 用于扩展webpack的功能。它直接作用于 webpack，扩展了它的功能。当然loader也时变相的扩展了 webpack ，但是它只专注于转化文件（transform）这一个领域。而plugin的功能更加的丰富，而不仅局限于资源的加载。

这样一解释基本就知道了loader和plugin功能定义上的区别了。  

### 还有更多(more)
其实webpack的知识点远不至于此，例如manifest、target、模块依赖等等，有很多东西只有在实际应用中才能了解，之后会在自己项目中应用下来深入下webpack这个工具。
