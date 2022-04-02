---
title: ife学习记录 - git文档阅读（git基础）
categories: git
tags: [git]
date: 2018-05-01
description: git 文档还是需要读一读的
---
# git文档阅读

## Basic - 基础
突然发现阅读文档后，还是有很多命令工作中不怎么用呀。不说了，记录下基础常用的吧。。。  
<!--more-->
看下基础command，基本上你们都能用到。
- ### 初始化新仓库
	要对现有的某个项目开始用 git 管理，只需到此项目所在的目录，执行：
	```bash
	$ git init
	```
	初始化后，在当前目录下会出现一个名为 .git 的目录，所有 git 需要的数据和资源都存放在这个目录中。
- ### 从现有仓库克隆
	如果想对某个开源项目出一份力，可以先把该项目的 git 仓库复制一份出来，这就需要用到 <code>git clone </code>命令。如果你熟悉其他的 VCS 比如 Subversion，你可能已经注意到这里使用的是 clone 而不是 checkout。这是个非常重要的差别，git 收取的是项目历史的所有数据（每一个文件的每一个版本），服务器上有的数据克隆之后本地也都有了。实际上，即便服务器的磁盘发生故障，用任何一个克隆出来的客户端都可以重建服务器上的仓库，回到当初克隆时的状态（虽然可能会丢失某些服务器端的挂钩设置，但所有版本的数据仍旧还在）。
	克隆仓库的命令格式为 <code>git clone [url]</code>。比如，要克隆 Ruby 语言的 git 代码仓库 Grit，可以用下面的命令：：
	```bash
	$ git clone git://github.com/schacon/grit.git
	```
	<b>划重点：</b>这会在当前目录下创建一个名为grit的目录，其中包含一个 .git 的目录，用于保存下载下来的所有版本记录，然后从中取出最新版本的文件拷贝。如果进入这个新建的 grit 目录，你会看到项目中的所有文件已经在里边了，准备好后续的开发和使用。如果希望在克隆的时候，自己定义要新建的项目目录名称，可以在上面的命令末尾指定新的名字：
	```bash
	$ git clone git://github.com/schacon/grit.git mygrit
	```
	唯一的差别就是，现在新建的目录成了 mygrit，其他的都和上边的一样。
- ### 提交更新到仓库
	要确定哪些文件当前处于什么状态，可以用 <code>git status </code>命令。如果在克隆仓库之后立即执行此命令，会看到类似这样的输出：
	```bash
	$ git status
	On branch master
	nothing to commit, working directory clean
	```
	然后，在当前目录添加一个readme文件，保存后运行 git status之后会看到类似这样的：
	```bash
	$ git status
	On branch master
	Untracked files:
	  (use "git add <file>..." to include in what will be committed)

	        readme

	nothing added to commit but untracked files present (use "git add" to track)
	```
	readme文件现在是untracked，我们要把它添加到暂存区，使用 <code>git add readme</code>：
	```bash
	$ git status
	On branch master
	Changes to be committed:
  	(use "git reset HEAD <file>..." to unstage)

        new file:   readme
	```
	现在的暂存区域已经准备妥当可以提交了。在此之前，请一定要确认还有什么修改过的或新建的文件还没有 git add 过，否则提交的时候不会记录这些还没暂存起来的变化。所以，每次准备提交前，先用 git status 看下，是不是都已暂存起来了，然后再运行提交命令 <code>git commit -m'new file readme'</code>:  
	```bash
	$ git commit -m "new file readme"
	[master 463dc4f] new file readme
	 2 files changed, 3 insertions(+)
	 create mode 100644 readme
	```

- ### 移除文件
	要从 git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。可以用 <code>git rm </code>命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。

	如果只是简单地从工作目录中手工删除文件，运行 git status 时就会在 “Changes not staged for commit” 部分（也就是未暂存清单）看到：  
	```bash
	$ rm readme
	$ git status
	On branch master
	Changes not staged for commit:
	  (use "git add/rm <file>..." to update what will be committed)
	  (use "git checkout -- <file>..." to discard changes in working directory)

	        deleted:    readme

	no changes added to commit (use "git add" and/or "git commit -a")
	```
	然后再运行 git rm 记录此次移除文件的操作：
	```bash
	$ git rm readme
	rm 'readme'
	$ git status
	On branch master
	Changes to be committed:
	  (use "git reset HEAD <file>..." to unstage)

	        deleted:    readme
	```
	最后提交的时候，该文件就不再纳入版本管理了。如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 -f（译注：即 force 的首字母），以防误删除文件后丢失修改的内容。
	<b>划重点：</b>另外一种情况是，我们想把文件从 git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中。换句话说，仅是从跟踪清单中删除。比如一些大型日志文件或者一堆 .a 编译文件，不小心纳入仓库后，要移除跟踪但不删除文件，以便稍后在 .gitignore 文件中补上，用 --cached 选项即可：
	```bash
	$ git rm --cached readme.txt
	```
- ### 查看提交历史
	在提交了若干更新之后，又或者克隆了某个项目，想回顾下提交历史，可以使用 git log 命令查看。
	```bash
	$ git log
	commit ca82a6dff817ec66f44342007202690a93763949
	Author: tanelmer <244lixinyi@163.com>
	Date:   Mon Mar 17 21:52:11 2018 -0700

    new file readme
	```
	<code>git log</code>还有很多参数，可以用help去查查，试试看看。  
- ### 回退文件/版本
	任何时候，你都有可能需要撤消刚才所做的某些操作。
	<code>git reset</code>这个命令用法很多，详细使用建议查文档，我工作用有到的，一般是回退版本，回退文件概率很少。  
	<code>git reset -h | git reset --help</code>
- ### 远程仓库
	远程仓库是指托管在网络上的项目仓库，可能会有好多个，其中有些你只能读，另外有些可以写。同他人协作开发某个项目时，需要管理这些远程仓库，以便推送或拉取数据，分享各自的工作进展。 管理远程仓库的工作，包括添加远程库，移除废弃的远程库，管理各式远程库分支，定义是否跟踪这些分支，等等。
	查看当前的远程库：  
	要查看当前配置有哪些远程仓库，可以用 <code>git remote</code> 命令，它会列出每个远程库的简短名字。在克隆完某个项目后，至少可以看到一个名为 origin 的远程库，git 默认使用这个名字来标识你所克隆的原始仓库。  
	添加远程库：  
	要添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用，运行 <code>git remote add [shortname] [url]</code>：  
	从远程仓库抓取数据： 
	<code>git fetch [remote-name]</code>  
	推送数据到远程仓库:  
	<code>git push origin master</code>  
	远程仓库的删除和重命名:  
	<code>git remote rename [old] [new]</code>  
	<code>git remote rm [name]</code>  
- ### 打标签
	同大多数 VCS 一样，git 也可以对某一时间点上的版本打上标签。人们在发布某个软件版本（比如 v1.0 等等）的时候，经常这么做。本节我们一起来学习如何列出所有可用的标签，如何新建标签，以及各种不同类型标签之间的差别。
	新建标签：  
	git 使用的标签有两种类型：轻量级的（lightweight）和含附注的（annotated）。轻量级标签就像是个不会变化的分支，实际上它就是个指向特定提交对象的引用。而含附注标签，实际上是存储在仓库中的一个独立对象，它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明，标签本身也允许使用 GNU Privacy Guard (GPG) 来签署或验证。一般我们都建议使用含附注型的标签，以便保留相关信息；当然，如果只是临时性加注标签，或者不需要旁注额外信息，用轻量级标签也没问题。
	含附注的标签：  
	创建一个含附注类型的标签非常简单，用 -a （译注：取 annotated 的首字母）指定标签名字即可：<code>git tag -a v1.4 -m 'my version 1.4'</code>
	而 -m 选项则指定了对应的标签说明，git 会将此说明一同保存在标签对象中。如果没有给出该选项，git 会启动文本编辑软件供你输入标签说明。
	轻量级标签:  
	轻量级标签实际上就是一个保存着对应提交对象的校验和信息的文件。要创建这样的标签，一个 -a，-s 或 -m 选项都不用，直接给出标签名字即可：<code>$ git tag [name]</code>
- ### 技巧和窍门
	<b>git 命令自动补全</b>  
	使用TAB键获取所有匹配的可用命令建议。  
	<b>git 命令别名</b>  
	git 并不会推断你输入的几个字符将会是哪条命令，不过如果想偷懒，少敲几个命令的字符，可以用 git config 为命令设置别名。来看看下面的例子：
	```bash
	$ git config --global alias.co checkout
	$ git config --global alias.br branch
	$ git config --global alias.ci commit
	$ git config --global alias.st status
	```
	现在，如果要输入 git commit 只需键入 git ci 即可。而随着 git 使用的深入，会有很多经常要用到的命令，遇到这种情况，不妨建个别名提高效率。
	
	---
	# 相关文章
	- [git documention](https://git-scm.com/docs)
	- [廖雪峰git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
	- [菜鸟教程](http://www.runoob.com/git/git-tutorial.html)