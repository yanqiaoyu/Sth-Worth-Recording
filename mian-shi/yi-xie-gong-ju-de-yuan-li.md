# 一些工具的原理

## 1. Fiddler原理

Fiddler的工作原理与中间人攻击非常类似，Fiddler是客户端与服务端中间的一层代理

客户端发送给服务端的HTTP或者HTTPS请求，其实是发给了Fiddler，而Fiddler，接受了这个请求并伪装成了客户端的请求发送给了服务器。

