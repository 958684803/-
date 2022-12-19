**Token的原理**

**Json Web Token(JWT)**
- Json Web Token(JWT)是一个费城轻巧的规范。这个规范许我们使用JWT在两个组织之间传递安全可靠的信息。
- 基于Token的身份验证是无状态的，我们不将用户的信息存放服务器中。这种概念解决了在服务器存储信息时的许多问题。NoSession意味着你的可以根据需求去增减机器，而不用去担心session数据的共享问题
![[Token分布式集群流程]]
**两种实现**:
- itsdangerous模块中的TimedJSONWebSignatureSerializer
- pyjwt模块:pip intall pyjwt

#验证Token的装饰器
当业务逻辑访问时，必须登录过才可以访问为了避免每次编写代码，我们可以编写一个装饰器来验证用户是否登录

**定义请求钩子**:
- 作用：可以在请求进来之前，先把token得到，然后还要验证token是否正确
在请求进来之前得到request携带的token，并且验证token