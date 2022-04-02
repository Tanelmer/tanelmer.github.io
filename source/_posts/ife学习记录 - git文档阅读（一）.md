---
title: ife学习记录 - git文档阅读（git配置）
categories: git
tags: [git]
date: 2018-05-01
description: git 文档还是需要读一读的
---
# git文档阅读

## Setup and Config - 安装与配置
这里主要还是学习配置项，安装没啥好说的。。  
<!--more-->
看下主要的配置项  
- ### 用户信息
	配置的是你个人的用户名称和电子邮件地址。这两条配置很重要，每次 git 提交时都会引用这两条信息，说明是谁提交了更新，所以会随更新内容一起被永久纳入历史记录：
	```bash
	$ git config --global user.name "tanelmer"
	$ git config --global user.email 244lixinyi@163.com
	```
- ### 文本编辑器
	设置默认使用的文本编辑器，git 需要你输入一些额外消息的时候，会自动调用一个外部文本编辑器给你用。默认会使用操作系统指定的默认编辑器，一般可能会是 Vi 或者 Vim。如果你有其他偏好，比如 Emacs 的话，可以重新设置：
	```bash
	$ git config --global core.editor emacs
	```
- ### 差异解决工具
	在解决合并冲突时使用哪种差异分析工具。比如要改用 vimdiff 的话：
	```bash
	$ git config --global merge.tool vimdiff
	```
- ### 查看配置信息
	查看已有配置信息列表 <code>$ git config --list</code>  
	```bash
	$ git config --list
	user.name=Scott Chacon
	user.email=schacon@gmail.com
	color.status=auto
	color.branch=auto
	color.interactive=auto
	color.diff=auto
	...
	```
	有时候会看到重复的变量名，那就说明它们来自不同的配置文件（比如 /etc/gitconfig 和 ~/.gitconfig），不过最终 git 实际采用的是最后一个。  
	也可以直接查阅某个环境变量的设定，只要把特定的名字跟在后面即可，像这样：
	```bash
	$ git config user.name
	tanelmer
	```

	---
	# 相关文章
	- [git documention](https://git-scm.com/docs)
	- [廖雪峰git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
	- [菜鸟教程](http://www.runoob.com/git/git-tutorial.html)