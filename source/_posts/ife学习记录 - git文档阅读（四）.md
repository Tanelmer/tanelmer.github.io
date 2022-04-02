---
title: ife学习记录 - git文档阅读（远程仓库）
date: 2018-05-01
categories: git
tags: [git]
description: git 文档还是需要读一读的
---
# git文档阅读

## 服务器上的Git - 远程仓库
远程仓库通常只是一个裸仓库（bare repository） — 即一个没有当前工作目录的仓库。
因为该仓库只是一个合作媒介，所以不需要从硬盘上取出最新版本的快照；仓库里存放的仅仅是 Git 的数据。简单地说，裸仓库就是你工作目录中 .git 子目录内的内容。
<!--more-->
### 关于远程仓库的常用git命令
   添加新的远程仓库  
    ```bash
    $ git remote add [shortname] [url]
    ```
   提取远程仓库  
   ```bash
    $ git fetch
   ```
   推送新分支到远程仓库  
    ```bash
    $ git push [alias] [branch]
    ```

### SSH KEY(公钥)
   由于你的本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以我们需要配置验证信息：  
    首先要在你的本地生成秘钥  
    ```bash
    $ ssh-keygen -t rsa -C "youremail@example.com"
    ```
   后面的 your_email@youremail.com 改为你在 github 上注册的邮箱，之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。
    成功的话会在~/下生成.ssh文件夹，里面有id_rsa.pub，复制里面的 key。
    ```bash
    $ cat ~/.ssh/id_rsa.pub
    ```
   然后我们需要到github你的账号settings里面SSH keys上添加你这台机器的本地公钥，相当于是你这台机器跟github账号的一个钥匙。  
    之后每次推送和拉取就不用再授权了。
    详细的操作可以看官方文档 [SSH github-help](https://help.github.com/articles/connecting-to-github-with-ssh/)

### git hooks(git钩子)
   钩子存放目录一般在.git/hooks。 Git 默认会放置一些脚本样本在这个目录中，除了可以作为挂钩使用，这些样本本身是可以独立使用的。
   所有的样本都是shell脚本，其中一些还包含了Perl的脚本，不过，任何正确命名的可执行脚本都可以正常使用 — 可以用Ruby或Python，或其他。
   在Git 1.6版本之后，这些样本名都是以.sample结尾，因此，你必须重新命名。在Git 1.6版本之前，这些样本名都是正确的，但这些样本不是可执行文件。
   当某些重要事件发生时，Git 以调用自定义脚本。  
   有两组挂钩：客户端和服务器端。客户端挂钩用于客户端的操作，如提交和合并。服务器端挂钩用于 Git 服务器端的操作，如接收被推送的提交。
   简单地来说，就是你在进行git操作是会有一些钩子，这些钩子允许你在git完成这些操作后调用脚本处理一些事情。  
   比如pre-commit钩子，post-merge钩子等。其实git也支持一些自定义钩子，
   一般用于持续集成、自动化部署。（最近才开始研究-。-还未深入）

   [你不知道的持续集成的3个Git Hooks详解](https://www.atlassian.com/continuous-delivery/git-hooks-continuous-integration)

   ---
# 相关文章
   - [git documention](https://git-scm.com/docs)
   - [廖雪峰git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
   - [菜鸟教程](http://www.runoob.com/git/git-tutorial.html)
