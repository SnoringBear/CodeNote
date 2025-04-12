# MagicOnion 库介绍

MagicOnion 是一个基于 gRPC 的 C#/.NET 实时通信框架，由日本游戏公司 Cysharp 开发。它特别适合需要双向实时通信的场景，如游戏服务器、聊天应用等。

## 核心特性

1. **基于 gRPC**：利用 HTTP/2 和 Protocol Buffers 的高性能
2. **无.proto文件**：使用 C# 接口定义服务，无需编写 proto 文件
3. **支持双向流**：Hub 系统实现服务器-客户端实时通信
4. **MessagePack 序列化**：默认使用高效的 MessagePack 序列化
5. **Unity 支持**：特别适合 Unity 游戏开发



## 安装

```bash
# 服务器端
Install-Package MagicOnion

# 客户端
Install-Package MagicOnion.Client
```

## 基本架构



### 1. 服务定义 (类似 gRPC 服务)

```csharp
public interface IGreeterService : IService<IGreeterService>
{
    UnaryResult<string> HelloAsync(string name);
    UnaryResult<int> SumAsync(int x, int y);
}
```



### 2. 服务实现

```csharp
public class GreeterService : ServiceBase<IGreeterService>, IGreeterService
{
    public async UnaryResult<string> HelloAsync(string name)
    {
        return $"Hello, {name}!";
    }

    public async UnaryResult<int> SumAsync(int x, int y)
    {
        return x + y;
    }
}
```



### 3. 服务器启动

```csharp
var server = new MagicOnionHostBuilder()
    .UseMagicOnion(
        new MagicOnionOptions(isReturnExceptionStackTraceInErrorDetail: true),
        new ServerPort("0.0.0.0", 12345, ServerCredentials.Insecure))
    .Build();

await server.RunAsync();
```



### 4. 客户端调用

```csharp
var channel = GrpcChannel.ForAddress("http://localhost:12345");
var client = MagicOnionClient.Create<IGreeterService>(channel);

var result = await client.HelloAsync("World");
Console.WriteLine(result); // 输出: Hello, World!
```



## 实时通信 Hub 系统

MagicOnion 提供了类似 SignalR 的 Hub 系统，支持双向实时通信。



### 1. 定义 Hub 接口

```csharp
public interface IChatHub : IStreamingHub<IChatHub, IChatHubReceiver>
{
    Task JoinAsync(string roomName, string userName);
    Task LeaveAsync();
    Task SendMessageAsync(string message);
}
```



### 2. 定义客户端接收接口

```csharp
public interface IChatHubReceiver
{
    void OnJoin(string name);
    void OnLeave(string name);
    void OnReceiveMessage(string name, string message);
}
```



### 3. 实现 Hub

```csharp
public class ChatHub : StreamingHubBase<IChatHub, IChatHubReceiver>, IChatHub
{
    private string roomName;
    private string userName;
    private IGroup room;

    public async Task JoinAsync(string roomName, string userName)
    {
        this.roomName = roomName;
        this.userName = userName;
        
        this.room = await this.Group.AddAsync(roomName);
        this.Broadcast(this.room).OnJoin(userName);
    }

    public async Task LeaveAsync()
    {
        await this.room.RemoveAsync(this.Context);
        this.Broadcast(this.room).OnLeave(this.userName);
    }

    public async Task SendMessageAsync(string message)
    {
        this.Broadcast(this.room).OnReceiveMessage(this.userName, message);
    }
}
```



### 4. 客户端使用 Hub

```csharp
var channel = GrpcChannel.ForAddress("http://localhost:12345");
var receiver = new ChatHubReceiver();
var client = await StreamingHubClient.ConnectAsync<IChatHub, IChatHubReceiver>(channel, receiver);

await client.JoinAsync("general", "user1");
await client.SendMessageAsync("Hello everyone!");

class ChatHubReceiver : IChatHubReceiver
{
    public void OnJoin(string name) => Console.WriteLine($"{name} joined");
    public void OnLeave(string name) => Console.WriteLine($"{name} left");
    public void OnReceiveMessage(string name, string message) => Console.WriteLine($"{name}: {message}");
}
```

## 与 Unity 集成

MagicOnion 特别适合 Unity 游戏开发：

1. 安装 Unity 包：
   - `MagicOnion.Client`
   - `MessagePack.UnityClient`
   - `Grpc.Tools` (代码生成)
2. 使用 `MagicOnion.Unity` 提供的 `GrpcChannelProvider` 管理连接

## 性能优势

1. 比传统 REST API 更高效
2. 比 SignalR 更轻量级
3. 二进制协议减少带宽使用
4. 支持流式传输

## 适用场景

- 实时游戏服务器
- 聊天应用程序
- 需要低延迟的双向通信系统
- Unity 客户端与 .NET 服务器的交互

## 注意事项

1. 需要 .NET 5+ 或 .NET Core 3.1+
2. 客户端和服务端必须共享相同的接口定义
3. 错误处理需要特别注意（不同于传统 C# 异常）
4. 生产环境应考虑安全认证

MagicOnion 结合了 gRPC 的高性能和 C# 的开发便利性，是构建实时系统的强大工具。