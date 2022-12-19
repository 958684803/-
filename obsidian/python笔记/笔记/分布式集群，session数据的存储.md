
- **session的工作原理**
![[session]]

- 客户端登陆用户信息  会收到服务器生成的sessionID，浏览器会将收到的sessionID保存到cookie中，之后的每次请求客户端就会把cookie里面的sessionID提交给服务器，服务器收到的sessionID会跟服务器内存里面的sessionID匹对，匹配成功了 说明还是当前这一次会话

- **分布式session工作原理**
	- 客户端登陆的时候浏览器中cookie保存的是server1中的sessionID
	- 如果用户在提交订单的时候分配到server2中，浏览器中没有保存server2的sessionID
	- server1和server2内存是不共享的
	- 解决办法是将sessionID存储一个缓存服务器里面
	- 我们使用[[Token]]来替代session
![[分布式 session]]