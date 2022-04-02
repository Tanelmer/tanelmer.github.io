---
title: ife学习记录 - 构建工具（一）
date: 2016-08-12
categories: 构建工具
tags: [构建工具]
description: 前端工程化，工具少不了，grunt、gulp、webpack、新出的parcel，一个接一个，先熟悉熟悉parcel和webpack。
---
# 构建工具（parcel）

## parcel - 快速，零配置的 Web 应用程序打包器
对于parcel这个新事物，莫名其妙的就在前端圈兴起了，目前为止star已超22K，之前虽然也有听过，但也没测试，我这边也就照着文档操作了一把，试一试感觉。
<!--more-->
![图片](https://picture-1256757196.cos.ap-chengdu.myqcloud.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-05-18%20%E4%B8%8B%E5%8D%888.36.08.png)
上面图片就是照着文档入门了一把，确实配置贼简单，写个入口文件就完事了。  
parcel中文文档中说了一些特性吧。
* 资源（意思是parcel基于资源进行打包，它会自动分析这些资源中的依赖关系，分别做不同的打包处理）
* 转换（内置postcss，babel这样一类的转换工具）
* 热编译（类似webpack-hot-middleware插件一样的功能，就是热编译）
* 代码拆分（这个特性其实就是支持模块化，按需或异步import导入都支持）
* 环境配置（对于生产环境的一些配置说明吧，看起来挺不错的）

总体来说parcel看起来确实比webpack简洁多了，有篇文章写的很透彻，测试了webpack和parcel一些数据对比，分析也挺有道理。其中有些坑我也遇到过。。
[Parcel Vs Webpack](https://segmentfault.com/a/1190000012612891)