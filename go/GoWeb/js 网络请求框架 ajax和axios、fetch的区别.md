# js 网络请求框架 ajax和axios、fetch的区别

**Ajax** 是异步 JavaScript 和 XML 的缩写，它是一种使用 JavaScript 在不刷新整个页面的情况下与服务器进行异步通信的技术。Ajax 并不是一个具体的 API，而是一系列技术的组合，包括：

- XMLHttpRequest 对象：用于发送和接收 HTTP 请求；
- JavaScript：用于处理服务器响应。

**Axios** 是一个基于 Promise 的 HTTP 请求库，它可以用于发送 GET、POST、PUT、DELETE 等各种 HTTP 请求。Axios 的优点包括：

- 语法简洁易懂；
- 支持 Promise，可以方便地处理异步请求；
- 功能丰富，支持拦截器、超时设置、取消请求等功能。

**Fetch** 是 ES6 中引入的原生 API，用于发送 HTTP 请求。Fetch 的优点包括：

- 语法简洁，比 Ajax 更易于使用；
- 基于 Promise，可以方便地处理异步请求；
- 支持 HTTP/2 协议。

**以下是 Ajax、Axios 和 Fetch 的详细比较：**

| 特性       | Ajax           | Axios    | Fetch                     |
| :--------- | :------------- | :------- | :------------------------ |
| 类型       | 技术统称       | 第三方库 | 原生 API                  |
| 依赖       | XMLHttpRequest | Promise  | Promise                   |
| 语法       | 复杂           | 简洁     | 简洁                      |
| 功能       | 基础           | 丰富     | 基础                      |
| 兼容性     | 良好           | 良好     | 较差 (低版本浏览器不支持) |
| 社区活跃度 | 高             | 高       | 高                        |
| 文档       | 完善           | 完善     | 完善                      |

drive_spreadsheet导出到 Google 表格

**总结：**

- Ajax 是传统的网络请求技术，兼容性好，但语法复杂，功能有限；
- Axios 是一个功能丰富的第三方库，语法简洁易懂，支持 Promise，是目前最流行的网络请求框架之一；
- Fetch 是 ES6 中引入的原生 API，语法简洁，支持 HTTP/2 协议，但兼容性较差。

**建议：**

- 如果需要兼容低版本浏览器，可以选择 Ajax；
- 如果需要丰富的功能和良好的社区支持，可以选择 Axios；
- 如果追求简洁的语法和最新的技术，可以选择 Fetch。