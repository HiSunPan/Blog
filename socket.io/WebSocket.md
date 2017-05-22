## WebSocket原理及技术简介
WebSocket用于在Web浏览器和服务器之间进行任意的双向数据传输的一种技术。WebSocket协议基于TCP协议实现，包含初始的握手过程，以及后续的多次数据帧双向传输过程。其目的是在WebSocket应用和WebSocket服务器进行频繁双向通信时，可以使服务器避免打开多个HTTP连接进行工作来节约资源，提高了工作效率和资源利用率。

### 引言
互联网发展的早期，网站上只是一些静态展示页面。用户请求(Request)网站页面，网站回复(Response)页面内容给用户浏览器。因为需求简单，所以也没有很复杂的协议过程。

随着互联网技术的发展，带宽逐步提高，用户数也越来越庞大。对互联网的呈现内容提出了要求，随之出现了动态页面技术，对同一个页面，页面的某些部分对不同的访问用户，呈现的内容不同。相关的实现技术有CGI、ASP、PHP、JSP等。由于访问量的增加，WEB服务器同时处理的用户数也达到了万(10K)以上级别，这就是C10K问题："The C10K problem"[1]。为了缓解服务器压力，每次Request/Response后连接(TCP连接)继续保持，以及对同一个TCP连接，多次复用Request/Response的方法(也称为Pipeline)也提了出来。这就是HTTP/1.1协议中长连接的主要内容。

伴随移动互联网的发展，大量移动终端和其上的APP应用接入网络，HTML5技术也提了出来，以便支持WEB上的音视频播放、实时游戏、实时聊天等。催生了这样一个需求，当服务器有更新时，需要立即将数据发送给客户端，这就是基于服务器端的推送技术。

WEBSOCKET之前的解决方法大概这么几种： 
* 轮询：客户端设置一个时间间隔，时间到以后，向服务器发送request询问有无新数据，服务器立即返回response，如果有更新则携带更新的数据。
* 长连接(long poll): 和轮询相似，但是为阻塞模式的轮询，客户端请求新的数据request, 服务器会阻塞请求，直到有新数据后才返回response给客户端；然后客户端再重复此过程。这两种方式的特点，不断的建立HTTP连接，然后发送请求request，之后服务器等待处理。服务端体现的是一种被动性，同时这种处理方式，非常耗费网络带宽和服务器资源。

服务器向客户端推送更新时，因为被动性，对低延迟的应用体验不好；因为request/response的交互方式，对网络带宽和服务器带来了额外的负担（例如多次请求的HTTP头部， TCP连接复用会导致的Head-of-Line Blocking线头阻塞[2]等）。如果在单一的TCP连接中，使用双向通信（全双工通信）就能很好的解决此问题。这就是WebSocket技术的缘由。

### WebSocket技术及协议

WebSocket技术的优点有：
1）通过第一次HTTP Request建立了连接之后，后续的数据交换都不用再重新发送HTTP Request，节省了带宽资源； 
2) WebSocket的连接是双向通信的连接，在同一个TCP连接上，既可以发送，也可以接收; 
3)具有多路复用的功能(multiplexing)，也即几个不同的URI可以复用同一个WebSocket连接。这些特点非常类似TCP连接，但是因为它借用了HTTP协议的一些概念，所以被称为了WebSocket。

### 产考资料

[MDN WebSocket](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)

[WebSocket原理及技术简介](http://blog.csdn.net/yinqingwang/article/details/52565133)

[Java后端WebSocket的Tomcat实现](http://www.cnblogs.com/doudouxiaoye/p/5656681.html)

[使用 WebSockets 的 9 个应用场景](http://www.oschina.net/translate/9-killer-uses-for-websockets)

