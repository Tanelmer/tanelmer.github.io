---
title: node-express搭建简单博客
date: 2019-01-14
categories: node
tags: [node,javascript,前端]
description: 用express框架和MongoDB来搭建个简单的博客系统
---
### node-express Blog 
node实战简单项目
<!-- more -->
## 需安装node、mongodb环境
两步开启服务
1. 开启MongoDB
我用的win系统，所以首先到mongo的安装目录下，cmd
例如：c:/Program Files/MongoDB/Server/3.4/bin<br>
<code>mongod --dbpath=D:\Node\Blog\db</code><br>
不知道开启mongo的，可以Google。<br>
mongo可视化工具：Robo 3T
2. 启动服务<br>
<code>node app.js</code>
***
目前只写了登录和注册，保存登录状态，其余功能还在建设中。。。
[github](https://github.com/Tanelmer/node-blog)