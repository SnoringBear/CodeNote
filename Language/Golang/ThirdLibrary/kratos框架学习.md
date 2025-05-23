#  kratos框架学习



### 一、新的工具



1、swagger后端API接口文档

```
Swagger目前是比较主流的RESTful风格的API文档工具,使用OpenAPI 规范
```



2、Tldraw

```
Tldraw 是一款开源的矢量绘图工具，可以用于绘制流程图、图表、示意图等。它可以部署在本地服务器或云服务器上，以便多人协作使用。
```



3、JWT 

```

JWT (JSON Web Token) 是一种用于在网络上安全地传输信息的方式。它是一种  开放标准 (RFC 7519)，定义了一种紧凑、自包含的方法来表示声明集。这些声明以 JSON 对象的形式存储，并使用数字签名进行验证。
```

JWT (JSON Web Token) 是一种用于在网络上安全地传输信息的方式。它是一种  **开放标准 (RFC 7519)**，定义了一种紧凑、自包含的方法来表示声明集。这些声明以 JSON 对象的形式存储，并使用数字签名进行验证。

这里是如何理解 JWT 的工作原理：

1. **创建**: 当用户成功登录您的应用程序时，服务器会创建包含用户声明（例如用户名、角色等）的 JSON 对象。然后，服务器使用一个秘密密钥对该对象进行签名，并将其与该对象一起编码成一个 JWT 字符串。
2. **传输**: JWT 字符串通常通过 HTTP 头或作为 URL 参数的一部分在客户端和服务器之间传输。由于 JWT 是自包含的，它不需要服务器存储有关用户的信息。
3. **验证**: 服务器收到 JWT 字符串后，会使用相同的秘密密钥对其进行解码和验证。如果签名有效，则服务器可以信任 JWT 中包含的声明并授予用户访问权限。

**以下是一些 JWT 的优点:**

- **安全性**: JWT 使用数字签名来确保信息的完整性和真实性。
- **可移植性**: JWT 可以轻松地在不同的服务器之间传输。
- **无状态**: 服务器不需要存储有关用户的信息，使其更具可扩展性。
- **紧凑**: JWT 是文本格式的，使得它们易于在网络上传输。

**以下是一些 JWT 的缺点:**

- **服务器端验证**: 服务器需要验证每个收到的 JWT，这可能会增加一些开销。
- **密钥泄露**: 如果秘密密钥泄露，攻击者可以伪造 JWT 并获得对应用程序的访问权限。
- **有限的存储**: JWT 不能存储大量数据。

**JWT 通常用于以下场景:**

- **用户身份验证**: JWT 用于在用户登录后保持会话状态。
- **API 授权**: JWT 用于授予客户端访问 API 资源的权限。
- **数据交换**: JWT 可以用于在不同的应用程序之间安全地交换数据。

**学习资源:**

- https://jwt.io/ - 一个在线工具，用于解码、验证和生成 JWT。
- https://en.wikipedia.org/wiki/JSON_Web_Token - JWT 的维基百科页面。
- https://www.npmjs.com/package//jsonwebtoken - 用于创建和验证 JWT 的流行 Node.js 库。
