---
title: ife学习记录 - git文档阅读（git分支）
categories: git
tags: [git]
date: 2018-05-01
description: git 文档还是需要读一读的
---
# git文档阅读

## Basic - 分支
分支操作应该是git-flow中最为常见的操作了，也是git作为分布式版本控制系统的关键所在。
<!--more-->
一天工作下来，切分支合分支，弄个几十次很正常吧，虽然这些操作都会，重新看看文档也没啥不好的。[廖雪峰 - 分支管理](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013743862006503a1c5bf5a783434581661a3cc2084efa000)
- ### 分支操作相关命令
	git鼓励大量使用分支：

	查看分支：git branch

	创建分支：git branch <name>

	切换分支：git checkout <name>

	创建+切换分支：git checkout -b <name>

	合并某分支到当前分支：git merge <name>

	删除分支：git branch -d <name>
- ### 解决冲突
	当两个分支修改了同一个文件的相同部分，如果进行分支合并，git就会报出冲突，需要你和另一个分支的开发者来共同决定需要哪个分支上的内容。  
	冲突文件中一般会看到类似下面这样的：  
	```bash
	Git is a distributed version control system.
	Git is free software distributed under the GPL.
	Git has a mutable index called stage.
	Git tracks changes of files.
	<<<<<<< HEAD
	Creating a new branch is quick & simple.
	=======
	Creating a new branch is quick AND simple.
	>>>>>>> test1	
	```
	Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容。
	当你解决完冲突后，需要重新将文件添加进暂存区，也就是进行add、commit操作。
	<b>tips:</b> 用git log --graph命令可以看到分支合并图。
- ### 分支策略
	在实际开发中，我们应该按照几个基本原则进行分支管理：
	首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
	那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；
	你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。所以，团队合作的分支看起来就像这样：
	<img src="https://cdn.liaoxuefeng.com/cdn/files/attachments/001384909239390d355eb07d9d64305b6322aaf4edac1e3000/0">
	Git分支十分强大，在团队开发中应该充分应用。

	---
	# 相关文章
	- [git documention](https://git-scm.com/docs)
	- [廖雪峰git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
	- [菜鸟教程](http://www.runoob.com/git/git-tutorial.html)