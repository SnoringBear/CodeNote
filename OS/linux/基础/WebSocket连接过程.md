WebSocket 连接过程包括握手阶段和数据传输阶段。以下是 WebSocket 连接的基本步骤：

1. **客户端发起连接请求：** WebSocket 连接是通过 HTTP 握手开始的。客户端通过向服务器发送一个 HTTP 请求来发起连接。这个请求包含了一些特殊的头部，其中一个是 `Upgrade` 头部，指定协议升级为 WebSocket。

   ```
   httpCopy codeGET /chat HTTP/1.1
   Host: example.com
   Upgrade: websocket
   Connection: Upgrade
   ```

2. **服务器响应握手：** 服务器接收到客户端的握手请求后，如果支持 WebSocket，会返回一个 HTTP 101 状态码表示协议切换成功，同时也会包含一些额外的头部信息。

   ```
   httpCopy codeHTTP/1.1 101 Switching Protocols
   Upgrade: websocket
   Connection: Upgrade
   ```

3. **建立 WebSocket 连接：** 握手成功后，连接从 HTTP 转为 WebSocket。此时，客户端和服务器可以通过相同的 TCP 连接进行双向通信。

4. **双方可以发送消息：** 一旦 WebSocket 连接建立，客户端和服务器都可以通过发送帧（frame）进行消息的传输。WebSocket 使用帧来封装数据，并支持文本帧和二进制帧。

   - **文本帧：** 用于传输文本数据。
   - **二进制帧：** 用于传输二进制数据。

   ```
   javascriptCopy code// 客户端发送文本帧
   connection.send("Hello, server!");
   
   // 服务器发送二进制帧
   connection.send(new Uint8Array([1, 2, 3, 4]));
   ```

5. **保持连接：** WebSocket 连接是全双工的，双方都可以随时发送消息。连接保持打开状态，直到其中一方关闭连接或发生错误。

6. **关闭连接：** 任何一方都可以通过发送关闭帧来关闭连接。关闭握手过程包括发送关闭帧、接收关闭帧、发送关闭确认帧。一旦关闭握手完成，连接就被关闭。

   ```
   javascriptCopy code// 客户端发送关闭帧
   connection.close();
   ```

总体而言，WebSocket 连接通过 HTTP 握手开始，然后升级为双向通信的 WebSocket 连接。这使得实时双向通信成为可能，适用于许多应用，如实时聊天、实时协作和实时数据推送。