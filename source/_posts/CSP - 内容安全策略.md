---
title: CSP - 内容安全策略
date: 2017-04-16
categories: 前端
tags: [前端安全]
description: XSS终结者 - CSP
---

## 关于对XSS终结者CSP的学习
昨天在团队中做了关于前端安全XSS CRSF相关的分享，内容里有涉及到CSP，说实话在写ppt时，也只是粗略的看了下CSP，而没有去详细了解，今天就详细的做下记录吧
<!-- more -->

## CSP概念
CSP 全称为 Content Security Policy，即内容安全策略。主要以白名单的形式配置可信任的内容来源，在网页中，能够使白名单中的内容正常执行（包含 JS，CSS，Image 等等），而非白名单的内容无法正常执行，从而减少跨站脚本攻击（XSS），当然，也能够减少运营商劫持的内容注入攻击。
***
### CSP发展时间轴
毋容置疑CSP是一个伟大的策略，但CSP从最初设计到被W3C认可制定成通用标准，却经历了一个漫长而曲折的过程。
1. CSP模型首次被提出
这要从2007年说起，当时XSS攻击已经在OWASP TOP10攻击中排名第一位，CSP的最初的设想就在这一年被Mozilla项目组的Gervase Markham和WEB安全界大牛Robert Hansen ‘rsnake’两人共同提出的。
2. 浏览器首次使用CSP
2011年3月Firefox 4.0发布，首次把CSP当作一种正式的安全策略规范使用到浏览器中。当时火狐使用的是自己定义的X-Content-Security-Policy 头。单从CSP推广上来看，Firefox4.0的发布是划时代的，虽然此时的CSP只是Firefox自己定义的一个内部标准。但在此之后，CSP的概 念被全球迅速推广。
3. Chrome使用CSP
随后在2011年9月，谷歌在Chrome浏览器14.0版本发布时加入CSP，而Chrome浏览器使用的也是自己的 CSP标准，它使用X-Webkit-CSP头进行对CSP的解析，这个头从字面上更能看出来Chrome浏览器使用的是Webkit内核。此时世界主流 的2大浏览器Chrome、Firefox都已经支持了CSP。
4. W3C起草CSP标准
作为标准发布的W3C组织顺其自然在2011年11月在官网上发布了CSP1.0草案。W3C的CSP1.0草案的语法和Firefox和Chrome中截然不同，随着时间的推移1年后，W3C的CSP1.0草案已经到了推选阶段，基本可以正式发布。
5. 全面支持W3C标准的CSP
在2012年2月Chrome25版本发布时，宣布支持W3C标准的CSP1.0。2013年6月Firefox宣布在 23版本中全面支持W3C的CSP1.0标准。同样是在2013年6月，W3C发布CSP1.1标准，里面又加入了不少语法，现在大多浏览器还都不支持。 IE10中开始支持CSP中的’sandbox’语法，其他语法暂不支持
***
## CSP类型
1. Content-Security-Policy
2. Content-Security-Policy-Report-Only
这两种策略类型的主要区别也可以从命名上看出，第一种对不安全的资源会进行阻止执行，而第二种只会进行数据上报，不会有实际的阻止。
当定义多个策略的时候，浏览器会优先采用最先定义的。
***
## CSP示例
示例：在 HTML 的 Head 中添加如下 Meta 标签，将在符合 CSP 标准的浏览器中使非同源的 不被加载执行。
```html
	<meta http-equiv="Content-Security-Policy" content="-src 'self'">
```
### HTML Meta 标签
　　在这种形式中，Meta 标签主要含有两部分的 key-value：http-equivcontent
	http-equiv 的 value 为 CSP 的策略类型，而 content 则是声明指令集合，即白名单。如
```html
	<meta http-equiv="Content-Security-Policy" content="-src 'self'">
```
　　在HTML 的 head 中 添加上面的 Meta 标签，那么当浏览器支持 CSP 标准时，由于使用的是 Content-Security-Policy 实际阻止的策略，所以将会使得非同源的 （根据指令集合来定）不会被加载及执行。
　　Meta 标签的 Content-Security-Policy-Report-Only 方式在当前（2016/5/19）多数移动端浏览器上表现正常，但是 不推荐 这样做，如 chrome 50 会产生如下的提示
　　The report-only Content Security Policy xxxxxxx was delivered via a element,which is disallowed. The policy has been ignored.
# HTTP Header
　　通过 Meta 的方式很是简单，但当涉及到的页面较多时，使用 Meta 标签的方式需要在每个页面都各自加上。而如果通过服务端配置 HTML 返回的响应头 HTTP header 带上 CSP 的指令的话，那将能够一劳永逸，同时支持多个页面。下图为响应头
　　不仅如此，这种形式的 Content-Security-Policy-Report-Only 方式能够得到更好的兼容支持，也是推荐方式。
***
## CSP属性指令
|指令	|取值示例|说明	|
| ------------- |:-------------:| -----:|
|default-src|	‘self’ cdn.example.com	|定义针对所有类型（js/image/css/web font/ajax/iframe/多媒体等）资源的默认加载策略，某类型资源如果没有单独定义策略，就使用默认。|
|-src	|‘self’ js.example.com	|定义针对Java的加载策略|
|object-src	|‘self’	|针对,, 等标签的加载策略|
|style-src	|‘self’ css.example.com	|定义针对样式的加载策略|
|img-src	|‘self’ image.example.com	|定义针对图片的加载策略|
|media-src	|‘media.example.com’	|针对或者引入的html多媒体等标签的加载策略|
|frame-src	|‘self’	|针对iframe的加载策略|
|connect-src|	‘self’	|针对Ajax、WebSocket等请求的加载策略。不允许的情况下，浏览器会模拟一个状态为400的响应|
|font-src	|font.qq.com	|针对Web Font的加载策略|
|sandbox	|allow-forms allow-s	|对请求的资源启用sandbox|
|report-uri	|/some-report-uri|	告诉浏览器如果请求的资源不被策略允许时，往哪个地址提交日志信息。不阻止任何内容，可以改用Content-Security-Policy-Report-Only头|
|base-uri	|‘self’	|限制当前页面的url（CSP2）
|child-src	|‘self’	|限制子窗口的源(iframe、弹窗等),取代frame-src（CSP2）
|form-action	|‘self’	|限制表单能够提交到的源（CSP2）
|frame-ancestors	|‘none’	|限制了当前页面可以被哪些页面以iframe,frame,object等方式加载（CSP2）
|plugin-types	|application/pdf	|限制插件的类型（CSP2）
***
### CSP实践经验
　　CSP 的阻止加载及执行的方式相当强大，也因为它如此强大，所以在使用时更是要小心谨慎，毕竟，如一个不小心制定了错误的指令集合方案，那将可能导致阻止了正常文件的加载，影响业务功能，这是相当危险的。
　　一步步制定你的 CSP 方案
　　1. 通过 HTML Meta 标签进行初步方案的制定
　　这种方式实现成本低，只对当前的 HTML 有效，从而能够进行逐步灰度。当然也存在上面提及的兼容性问题，但如果现在是在移动端上，或者在可预期的浏览器内核上跑的话，在兼容性满足的情况下，那还是可以通过这个方式进行 Report-Only。结合自己业务的资源情况以及在 Chrome 上调试制定初步方案。
　　2. 使用 HTTP Header 的 Content-Security-Policy-Report-Only 方式进一步确定方案
　　由于上面的 Meta 标签存在一定的兼容问题，所以当我们制定了初步方案后，就可以开始使用 HTTP Header 的形式，小心驶得万年船，这里还是建议先使用 Report-Only 的方式，并指定上报的 url 来收集阻止的内容，通过上报的数据进行方案的优化，从而进一步确定具体方案。
　　3. HTTP Header 改用 Content-Security-Policy 策略进行实际拦截阻止
　　具体的 CSP 方案经过上面两轮洗礼，在分析完上报的数据，确定百无疏漏后，可将HTTP Header 改用 Content-Security-Policy 策略，从而进行实际拦截阻止。
### 相关链接
[XSS终结者-CSP理论与实践](http://mt.sohu.com/20160620/n455409002.shtml)
[CSP 策略指令](https://developer.mozilla.org/zh-CN/docs/Web/Security/CSP/CSP_policy_directives)
