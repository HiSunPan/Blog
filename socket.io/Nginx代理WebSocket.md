要在企业产品中使用WebSockets，为满足高性能和高可用性，须要多个WebSocketserver。负载均衡层须要支持WebSocket协议。

Nginx从1.3版起就開始支持WebSocket协议，并且能够担当WebSocket应用程序的反向代理以及实现负载均衡。

WebSocket协议和HTTP协议不同，可是WebSocket协议的握手和HTTP是兼容的，它使用HTTP的Upgrade协议头将连接从HTTP连接升级到WebSocket连接。

这个特性使得WebSocket应用程序能够非常easy地应用到现有的基础设施。比如，WebSocket应用能够使用标准的80和443 HTTPport，因此能够通过现有的防火墙设施。

WebSockets应用程序会在client和server之间建立一个长连接，使得开发实时应用非常easy。

HTTP的Upgrade协议头机制用于将连接从HTTP连接升级到WebSocket连接。Upgrade机制使用了Upgrade协议头和Connection协议头。反向代理server在支持WebSocket协议方面面临着一些挑战。挑战之中的一个是WebSocket是一个逐段转发（hop-by-hop）协议。因此当代理server拦截到来自client的Upgrade请求时，代理server须要将自己的Upgrade请求发送给后端server，包含适合的请求头。并且。由于WebSocket连接是长连接，与传统的HTTP端连接截然不同，故反向代理server还须要同意这些连接处于打开（Open）状态，而不能由于其空暇就关闭了连接。

### Nginx代理设置
```shell
location /chat/ {
    proxy_pass http://backend;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```
> 总结：WebSocket连接是由HTTP连接升级来的，header中Connection为“upgrade”说明这个链接需要升级，并用Upgrade 指定升级的链接类型，这里是websocket。

### 产考资料(
[Nginx-WebSocket proxying](http://nginx.org/en/docs/http/websocket.html)

[Nginx担当WebSockets代理](http://www.cnblogs.com/mfrbuaa/p/5413786.html)

[Websocket协议之握手连接](http://www.cnblogs.com/oshyn/p/3574497.html)