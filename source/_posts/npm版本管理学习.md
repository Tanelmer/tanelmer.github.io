---
title: 聊一聊npm版本管理
date: 2020-04-24
categories: node
tags: [node,javascript,前端]
description: npm版本管理规范
---
> 一段对话  
-。- ：'npm有发过模块包吗？'  
=、= ：'发过一些。'  
-。- ：'那知道npm版本如何管理的吗？版本号的规则呢？'  
=、= ：'？？？'

<!--more-->
## npm版本管理 - SemVer
这个[SemVer](https://github.com/semver/semver/blob/master/semver.md)是npm模块采用的语义化版本控制规范。  
### 基本格式：  

| Major | Minor | Patch |
|---|---|---|
| 主版本号 | 次版本号 | 修订版本号 |
|x|y|z|

>Patch version Z (x.y.Z | x > 0) MUST be incremented if only backwards compatible bug fixes are introduced. A bug fix is defined as an internal change that fixes incorrect behavior.  

>Minor version Y (x.Y.z | x > 0) MUST be incremented if new, backwards compatible functionality is introduced to the public API. It MUST be incremented if any public API functionality is marked as deprecated. It MAY be incremented if substantial new functionality or improvements are introduced within the private code. It MAY include patch level changes. Patch version MUST be reset to 0 when minor version is incremented.

>Major version X (X.y.z | X > 0) MUST be incremented if any backwards incompatible changes are introduced to the public API. It MAY also include minor and patch level changes. Patch and minor version MUST be reset to 0 when major version is incremented.

全是英文~~那就只好机器翻译了，总的来说就是。。
1. 主版本需要更改：是当你新增向下不兼容的功能，大版本更新
2. 次版本需要更改：是当你新增了向下兼容的功能
3. 修订版本需要更改：是当你修复了某些有问题的功能


### 基本规则
<code>"hexo": "^3.2.0"</code>像这种版本号前面的~、^符号，代表啥意思呢？  
>"3.2.0"：指定版本  
"~3.2.0"：小版本迭代，如果有缺省值，缺省部分任意迭代；
如果没有缺省值，只允许补丁即修订版本（Patch）的迭代  
"^3.2.0"：大版本迭代，允许从左到右的第一段不为0那一版本位+1迭代（左闭右开）；如果有缺省值，且缺省值之前没有不为0的版本位，则允许缺省值的前一位版本+1迭代

这块这一篇[文章](https://www.cnblogs.com/skylor/p/9675646.html)有很多举例，讲的也很透彻。我就mark一下，以防以后忘记了又要到处找资料。。。