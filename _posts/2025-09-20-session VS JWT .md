---
title: 【未完结】session VS JWT
date: 2025-09-20 16:00:00 +0800
categories: [前端]
tags: 
---

### 前情

HTTP 协议是无状态协议，一次性对话，服务器不会存储用户的身份信息。想要“输入一次账密、就能一直畅快购物”，就需额外的机制，来帮我们存储自己的身份信息（会话数据？）。

> 如果将会话数据发送回客户端（如浏览器或移动应用程序），则存在安全问题，因为可能会有人访问或拦截会话数据并更改数据来访问服务器。此外，在客户端和服务器之间可能有大量的数据来回传输，因此需要将其存储在后端，通常是数据库中。



### Session 认证

> “将登入的信息**存在服务器上**。”

前端拿到的 Session id 是**由服务器生成、存放在资料库中后，再回传给前端的**。

前端收到 Session id 时，前端就可以将该 Session id 的值**存放在 Cookie 中**，并在后续使用。

### JWT 认证

> “将登入信息**存在前端 Client 上**。”

服务器会**生成一个 JWT 格式的 Token，并直接返回给前端**、不需要在服务器中存储该 Token。

> 其主要思想是将用户信息存储在会话令牌本身中。
>
> 为了确保其安全性，令牌的一部分使用服务器唯一知道的密钥进行了签名。因此，即使客户端和服务器可以看到令牌的用户信息部分，第二部分（已签名的部分）只能由服务器进行验证。

只要 JWT 签名验证成功，后端就会**无条件相信 JWT Token 中的内容**。

#### 后端为何能无条件相信？



#### JWT 令牌结构

- **Header（头部）：**是一个 JSON 对象，用来存放实际需要传递的数据。JWT 规定了7个官方字段，供选用。
- Payload（负载）
- **Signature（签名）：**对前两部分的签名，防止数据篡改。

JWT 默认是不加密的。

> 在 [Node.js](http://apifox.com/apiskills/how-to-install-nodejs/) 中使用 JWT，你需要了解以下基本概念：
>
> 1. **JWT 结构**： JWT 由三部分组成，分别是头部（Header）、载荷（Payload）和签名（Signature）。头部包含算法和令牌类型，载荷包含要传递的信息，签名用于验证令牌的真实性。
> 2. 密钥： 生成和验证 JWT 时，你需要一个密钥。密钥可以是对称密钥（使用相同的密钥进行签名和验证）或非对称密钥（使用不同的密钥进行签名和验证）。
> 3. 签名验证： 接收 JWT 的服务器使用密钥验证签名以确保 JWT 未被篡改。如果签名验证成功，服务器可以信任 JWT 中的信息。
>
> ——[Node.js 中如何进行 JWT(JSON Web Tokens)身份验证和授权？](https://apifox.com/apiskills/how-to-use-jwt-in-nodejs/)



#### 简单实现 JWT

（想用 Node.js 来实现后端服务器，so 开始入门 Node.js——更深入の知识请见别的文件。）

<https://dvmhn07.medium.com/jwt-authentication-in-node-js-a-practical-guide-c8ab1b432a49>

- [node中使用jwt实现token身份验证](https://juejin.cn/post/6993720961308557325)

课外参考：<https://www.reddit.com/r/node/comments/1bys7mb/how_to_implement_a_magic_link_authentication/?tl=zh-hans>

------

### 参考：

[Session 和 JWT 的差別在哪裡？](https://kucw.io/blog/session-vs-jwt/)

[JWT真的安全吗？如何解决该问题](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&ved=2ahUKEwiRz5DXtOSPAxV4EjQIHchKOqIQFnoECCIQAQ&url=https%3A%2F%2Fjuejin.cn%2Fpost%2F7238921098808475705&usg=AOvVaw0CQfH15neaJO3EIDOsUO59&opi=89978449)

[JSON Web Token 入门教程- 阮一峰的网络日志](https://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)