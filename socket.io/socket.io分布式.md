 socket.io是目前较为流行的web实时推送框架，其基于nodejs语言开发，底层用engine.io实现。 借助nodejs语言异步的特性，其获得了不错的性能。但单个实例的socket.io依然承载能力有限，最多只能容纳3000个long-polling方式的客户端进行连接。

### 将socket.io进行分布式扩展的难点有两处：

1. 进行负载均衡时客户端必须保证始终连到一个节点上

如果客户端采用long-polling长轮训方式进行连接，则每次轮训都会产生一个新的请求，若不进行限制。就有可能连接到集群内新的 socket.io节点上，导致异常的发生。

解决方法：使用nginx的ip_hash实现session sticky，让客户端始终连接到集群内一台节点上。

2. 多个实例之间的消息推送

当集群内某台节点想要向连接到集群的所有客户端发送消息时，某些客户端因为负载均衡时ip_hash可能被分配到了其他的节点上，这时就需要向其他节点发布推送消息，让其他节点的同时向客户端进行推送。

解决方法：使用Redis的发布与订阅功能与[socket.io-redis](https://github.com/socketio/socket.io-redis)开源库，该库在节点向客户端群发消息时会将该消息发布到redis的订阅队列中，让其他节点能够订阅到该消息，从而实现节点间消息推送。

### 其他注意点

* 由于nginx的反向代理机制和socket.io的自动重连机制，上述架构还具备高可用的特性，即当某个节点down机时，原先连接到该节点上的客户端会自动重连至其它节点上。

* 节点的数量可以随时增减，不需要暂停服务，只需修改nginx配置即可。

* nginx的ip_hash是基于ip的前三段进行计算的，也就是说ip只有D段不同的两台客户端一定会连接到同一台服务器上，这点测试的时候需要注意。

* 可以通过redis的订阅发布服务来实现其他系统同集群的通信，完成集群的管理工作。

* 由于是分布式环境，所以节点内存中存储的信息（如用户、房间信息）可以考虑持久化到redis或MongoDB中。



### 产考资料(

[socket.io](https://socket.io)

[Socket.io的集群方案](https://my.oschina.net/swingcoder/blog/527648)

[socket.io分布式](http://blog.csdn.net/kelong_xhu/article/details/50846483)

