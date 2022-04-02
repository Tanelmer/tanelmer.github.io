---
title: websocket之即时通讯
date: 2017-06-22
categories: 前端
tags: [HTTP,javascript,前端]
description: html5新增websocket协议研究。。
---
# websocket学习研究记录
1. 什么是websocket
2. websocket出现前的即时通讯技术
3. websocket即时通讯，socket.io
4. mqtt了解
5. 简单实践一 - 在线聊天室
<!-- more -->

---
## 什么是websocket？
>WebSocket是HTML5开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。
WebSocket协议支持（在受控环境中运行不受信任的代码的）客户端与（选择加入该代码的通信的）远程主机之间进行全双工通信。用于此的安全模型是Web浏览器常用的基于原始的安全模式。 协议包括一个开放的握手以及随后的TCP层上的消息帧。 该技术的目标是为基于浏览器和服务器进行双向通信的（服务器不能依赖于打开多个HTTP连接（例如，使用XMLHttpRequest或iframe和长轮询））应用程序提供一种通信机制。


 从引用看来websokcet是一种网络通讯协议，它实现了浏览器与服务器全双工(full-duplex)通信——允许服务器主动发送信息给客户端。
 既然是网络通讯协议，那它跟http有啥关系呢？
 按我的理解吧，它们基本没多大关系，要有关系也只能说都基于tcp协议来建立连接的。
 也有些人把它看做是对于http协议的完善，仁者见仁智者见智吧。

---
## websocket出现前的即时通讯方案
 websocket出现前的即时通讯方案：ajax短轮询，comet
 两个名词，<code>ajax短轮询</code>、<code>comet</code>
 1.	ajax短轮询
 	ajax 定时（可以使用JS的 setTimeout 函数）去服务器查询是否有新消息，从而进行增量式的更新。这种方式间隔多长时间再查询是个问题，因为性能和即时性是反比关系。间隔太短，海量的请求会拖垮服务器，间隔太长，服务器上的新数据就需要更长的时间才能到达客户机。
	优点：逻辑简单；
	缺点：大多数请求是无效请求，在轮询很频繁的情况下对服务器的压力很大；
 2. comet - 一种hack技术
 > 以即时通信为代表的web应用程序对数据的Low Latency要求，传统的基于轮询的方式已经无法满足，而且也会带来不好的用户体验。于是一种基于<code>http长连接</code>的“服务器推”技术便被hack出来。这种技术被命名为Comet，这个术语由Dojo Toolkit 的项目主管Alex Russell在博文Comet: Low Latency Data for the Browser首次提出，并沿用下来。

 <b>原理</b>：http长连接。客户端与服务器端保持一个长连接，只有客户端需要的数据更新时，服务器才主动将数据推送给客户端。
 comet实现又有两种方式：一种是基于ajax的长轮询，一种是基于隐藏的iframe流技术。

 	- ajax的长轮询(long polling)
 	浏览器发出XMLHttpRequest 请求，服务器端接收到请求后，会阻塞请求直到有数据或者超时才返回，浏览器JS在处理请求返回信息（超时或有效数据）后再次发出请求，重新建立连接。在此期间服务器端可能已经有新的数据到达，服务器会选择把数据保存，直到重新建立连接，浏览器会把所有数据一次性取回。
	![ajax长轮询图解](http://www.52im.net/data/attachment/forum/201605/26/145816g98sb33ceq2669my.jpg)
 	  - 优点：任意浏览器都可用，实时性好，无消息的情况下不会进行频繁的请求
	  - 缺点：连接创建销毁操作还是比较频繁，服务器维持着连接比较消耗资源
	  - 案例：微信网页版（设置了25s超时时间）
 	- 流技术
 	在页面中放一个隐藏的iframe，iframe的src 属性设为对一个长连接的请求，服务器端则可以不停地返回数据，相对于第一种方式，这种方式跟传统的服务器推则更接近。在第一种方式中，浏览器在收到数据后会直接调用JS回调函数，但是这种方式该如何响应数据呢？可以通过在返回数据中嵌入JS脚本的方式，如“<code>&lt;script type="text/javascript"&gt; js_func(“data from server ”) &lt;/script></code>”，服务器端将返回的数据作为回调函数的参数，浏览器在收到数据后就会执行这段JS脚本。每次数据传送不会关闭连接，连接只会在通信出现错误时，或是连接重建时关闭（一些防火墙常被设置为丢弃过长的连接， 服务器端可以设置一个超时时间， 超时后通知客户端重新建立连接，并关闭原来的连接）。
	![流技术图解](http://www.52im.net/data/attachment/forum/201605/26/145819wllzzlwr5ee5oq0v.jpg)
 	  - 优点：消息能够实时到达；
	  - 缺点：使用 iframe 请求一个长连接有一个很明显的不足之处：IE、Morzilla Firefox 下端的进度栏都会显示加载没有完成，而且 IE 上方的图标会不停的转动，表示加载正在进行；
	  - 案例：Google公司在一些产品中使用了iframe流，如Google Talk。
---
## websocket即时通讯，socket.io
 ** 首先我们看下ws通用方案架构：**
 ![ws架构](http://www.52im.net/data/attachment/forum/201605/25/172320ln46d76hnzz9dht9.png)
 然后再是ws技术原理：
 ![ws技术原理](http://www.52im.net/data/attachment/forum/201605/25/172321pg747a7ru4lghzwu.png)
 客户端发起ws请求：
 ```javascript
 // javacsript
  var ws = new WebSocket("ws://127.0.0.1:4000");
  ws.onopen = function(){ //连接建立成功回调
    console.log("succeed");
  };
  ws.onerror = function(){ //失败回调
    console.log(“error”);
  };
  ws.onmessage = function(e){ //接受到消息回调
  //处理消息等逻辑
  console.log(e); 
  }
  ```
 向浏览器发送ws请求
 ```javascript
 // Connection必须为：Upgrade，表示client希望升级连接；
 // Upgrade必须为：websocket，表示client希望升级到Websocket协议；
 // Sec-WebSocket-Key：是随机字符串，服务端会将其做一定运算，最后在Response中返回“Sec-WebSocket-Accept”头的值。用于避免普通http请求被当做WebSocket协议。
 // Sec-WebSocket-Version：表示支持的Websocket版本。RFC6455要求使用的版本是13，之前草案的版本均应当被弃用。
 ```
 ![ws请求](http://www.52im.net/data/attachment/forum/201605/25/172456rjjj5efocmkrhcmr.png)

 然后问题来了。。
 切换后的websocket 协议中的 数据传输帧的格式(此时不再使用html协议) 官方文档给出的说明：（完全看不懂啊，一脸懵逼呀）
 ![ws数据传输帧](http://www.52im.net/data/attachment/forum/201605/25/172735e5ht85cz5ti7hl5m.png)
 找了一张大神图解解释下
 ![ws数据传输帧](http://www.52im.net/data/attachment/forum/201605/25/172824yh64cwi5gdoovhj2.png)

 这边我在实践中见到的只有Payload data字段，就是传输的数据主体。 
 对于具体数据帧frame的解释，以后再深入了解。

 ** socket.io - 支持WebSocket、用于WEB端的即时通讯的框架**

 socket.IO是一个完全由JavaScript实现、基于Node.js、支持WebSocket的协议用于实时通信、跨平台的开源框架，它包括了客户端的JavaScript和服务器端的Node.js。socket.io除了支持WebSocket通讯协议外，还支持许多种轮询（Polling）机制以及其它实时通信方式，并封装成了通用的接口，并且在服务端实现了这些实时机制的相应代码。socket.io实现的Polling通信机制包括Adobe Flash Socket、AJAX长轮询、AJAX multipart streaming、持久Iframe、JSONP轮询等。socket.io能够根据浏览器对通讯机制的支持情况自动地选择最佳的方式来实现网络实时应用。当前，socket.io最新版本是于2015年1月19日发布的1.3.0版本，该版本增强了稳定性和提高了性能，并修复了大量Bug。

 socket.io设计的目标是构建能够在不同浏览器和移动设备上良好运行的实时应用，如实时分析系统、二进制流数据处理应用、在线聊天室、在线客服系统、评论系统、WebIM等。目前，socket.io已经支持主流PC浏览器(如IE、Safari、Chrome、Firefox、Opera等)和移动平台上的浏览器（iOS平台下的Safari、Android平台下的基于Webkit的浏览器等）。

 socket.io已经具有众多强大功能的模块和扩展API，如（session.socket.io)（http session中间件，进行session相关操作）、socket.io-cookie（cookie解析中间件）、session-web-sockets（以安全的方式传递Session）、socket-logger（JSON格式的记录日志工具）、websocket.MQ（可靠的消息队列）、socket.io-mongo（使用MongoDB的适配器）、socket.io-redis（Redis的适配器）、socket.io-parser（服务端和客户端通讯的默认协议实现模块）等。

 socket.io实现了实时、双向、基于事件的通讯机制,它解决了实时的通信问题，并统一了服务端与客户端的编程方式。启动了Socket以后，就像建立了一条客户端与服务端的管道，两边可以互通有无。它还能够和Express.js提供的传统请求方式很好的结合，即可以在同一个域名，同一个端口提供两种连接方式：

 request/response, websocket(flashsocket,ajax…).

 demo代码
```javascript
// Setup basic express server
var express = require('express');
var app = express();
var path = require('path');
var server = require('http').createServer(app);
var io = require('../..')(server);
var port = process.env.PORT || 3000;

server.listen(port, function () {
  console.log('Server listening at port %d', port);
});

// Routing
app.use(express.static(path.join(__dirname, 'public')));

// Chatroom

var numUsers = 0;

io.on('connection', function (socket) {
  var addedUser = false;

  // when the client emits 'new message', this listens and executes
  socket.on('new message', function (data) {
    // we tell the client to execute 'new message'
    socket.broadcast.emit('new message', {
      username: socket.username,
      message: data
    });
  });

  // when the client emits 'add user', this listens and executes
  socket.on('add user', function (username) {
    if (addedUser) return;

    // we store the username in the socket session for this client
    socket.username = username;
    ++numUsers;
    addedUser = true;
    socket.emit('login', {
      numUsers: numUsers
    });
    // echo globally (all clients) that a person has connected
    socket.broadcast.emit('user joined', {
      username: socket.username,
      numUsers: numUsers
    });
  });

  // when the client emits 'typing', we broadcast it to others
  socket.on('typing', function () {
    socket.broadcast.emit('typing', {
      username: socket.username
    });
  });

  // when the client emits 'stop typing', we broadcast it to others
  socket.on('stop typing', function () {
    socket.broadcast.emit('stop typing', {
      username: socket.username
    });
  });

  // when the user disconnects.. perform this
  socket.on('disconnect', function () {
    if (addedUser) {
      --numUsers;

      // echo globally that this client has left
      socket.broadcast.emit('user left', {
        username: socket.username,
        numUsers: numUsers
      });
    }
  });
});
``` 
 [socket.io官网](https://socket.io/)

---
## mqtt了解
> MQTT（Message Queuing Telemetry Transport，消息队列遥测传输）是IBM开发的一个即时通讯协议，有可能成为物联网的重要组成部分。该协议支持所有平台，几乎可以把所有联网物品和外部连接起来，被用来当做传感器和制动器（比如通过Twitter让房屋联网）的通信协议。
	MQTT协议是为大量计算能力有限，且工作在低带宽、不可靠的网络的远程传感器和控制设备通讯而设计的协议，它具有以下主要的几项特性：
	1、** 使用发布/订阅消息模式，提供一对多的消息发布，解除应用程序耦合 **；
	2、对负载内容屏蔽的消息传输；
	3、 ** 使用 TCP/IP 提供网络连接  **；
	4、有三种消息发布服务质量：
	“至多一次”，消息发布完全依赖底层 TCP/IP 网络。会发生消息丢失或重复。这一级别可用于如下情况，环境传感器数据，丢失一次读记录无所谓，因为不久后还会有第二次发送。
	“至少一次”，确保消息到达，但消息重复可能会发生。
	“只有一次”，确保消息到达一次。这一级别可用于如下情况，在计费系统中，消息重复或丢失会导致不正确的结果。
	5、小型传输，开销很小（固定长度的头部是 2 字节），协议交换最小化，以降低网络流量；
	6、使用 Last Will 和 Testament 特性通知有关各方客户端异常中断的机制；

 mqtt是未来lot通讯的技术支持，我在业务中用到的只是它支持ws连接的即时通讯，我感觉是大财小用了，用socket.io也能解决即时通讯问题。
 说说学到的东西吧，mqtt ws模块的相关api，发布订阅模式，话题层级规范。
 mqtt ws相关api代码基本跟socket.io里面类似，这边不再赘述了。
 发布订阅模式
 了解过设计模式的同学应该也知道，这种模式与http请求响应不同，它解耦了发布者与订阅者之间的关系，这就表示，发布者和订阅者不用直接建立联系，可以通过一个中间模块来处理发布和订阅之间的关系。
  - 发布者与订阅者不比了解彼此，只要认识同一个消息代理即可。
  - 发布者和订阅者不需要交互，发布者无需等待订阅者确认而导致锁定。
  - 发布者和订阅者不需要同时在线，可以自由选择时间来消费消息。


 话题层级规范
 MQTT是通过主题对消息进行分类的，本质上就是一个UTF-8的字符串，不过可以通过反斜杠表示多个层级关系。主题并不需要创建，直接使用就是了。
 主题还可以通过通配符进行过滤。其中，+可以过滤一个层级，而*只能出现在主题最后表示过滤任意级别的层级。举个例子：
 - building-b/floor-5：代表B楼5层的设备。
 - +/floor-5：代表任何一个楼的5层的设备。
 - building-b/*：代表B楼所有的设备。
 注意，MQTT允许使用通配符订阅主题，但是并不允许使用通配符广播。

---
## 简单实践一 - 在线聊天室
用了node express搭的服务器，还有socket.io，简单的搭了个im在线聊天demo。
[github - demo](https://github.com/Tanelmer/node-chat)

---
# 相关文章
 [im开发者社区](http://www.52im.net/thread-336-1-1.html)
 [http长连接 短连接](https://www.cnblogs.com/cswuyg/p/3653263.html)
 [web即时通讯](http://blog.csdn.net/u010297957/article/details/60878821)
 [mqtt入门 - 知乎专栏](https://zhuanlan.zhihu.com/p/20888181)
 [mqtt协议中文版](https://www.gitbook.com/book/mcxiaoke/mqtt-cn/details)
