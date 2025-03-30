# MessagePack for C# 库介绍

MessagePack 是一种高效的二进制序列化格式，类似于 JSON 但更小更快。在 C# 中，有几个流行的 MessagePack 实现：

## 官方推荐库：MessagePack-CSharp

GitHub: https://github.com/MessagePack-CSharp/MessagePack-CSharp

### 特点

- 高性能二进制序列化
- 零内存分配的极速序列化
- 支持 .NET Standard 2.0/2.1 和 .NET 5/6/7/8+
- 支持 LZ4 压缩

### 安装

bash

复制

```
Install-Package MessagePack
Install-Package MessagePack.Annotations
Install-Package MessagePack.ReactiveProperty
Install-Package MessagePack.UnityShims
```

### 基本用法

1. 定义可序列化的类：

csharp

复制

```
[MessagePackObject]
public class Person
{
    [Key(0)]
    public int Age { get; set; }
    
    [Key(1)]
    public string Name { get; set; }
    
    [Key(2)]
    public List<string> Hobbies { get; set; }
}
```

1. 序列化和反序列化：

csharp

复制

```
var person = new Person { Age = 30, Name = "John", Hobbies = new List<string> { "Reading", "Swimming" } };

// 序列化
byte[] bytes = MessagePackSerializer.Serialize(person);

// 反序列化
var person2 = MessagePackSerializer.Deserialize<Person>(bytes);
```

### 高级特性

- **压缩支持**：

csharp

复制

```
var options = MessagePackSerializerOptions.Standard.WithCompression(MessagePackCompression.Lz4BlockArray);
byte[] compressedBytes = MessagePackSerializer.Serialize(person, options);
```

- **自定义解析器**：

csharp

复制

```
[MessagePackObject]
public class CustomClass
{
    [SerializationConstructor]
    public CustomClass(int x, int y)
    {
        this.X = x;
        this.Y = y;
    }
    
    [Key(0)]
    public int X { get; }
    
    [Key(1)]
    public int Y { get; }
}
```

## 其他 C# MessagePack 实现

1. **msgpack-cli** (较旧的实现)
   - GitHub: https://github.com/msgpack/msgpack-cli
2. **Neo.IronLua** 中的 MessagePack 实现

## 性能比较

MessagePack-CSharp 通常比其他 C# 序列化库（如 JSON.NET 或 BinaryFormatter）快得多，且生成的二进制数据更小。

## 使用场景

- 网络通信协议
- 游戏开发中的数据序列化
- 高性能分布式系统
- 需要减少序列化大小和时间的应用

## 注意事项

- 需要为所有要序列化的类型添加适当的属性
- 版本兼容性需要谨慎处理（添加新字段时使用新的 Key 值）
- 二进制格式不如 JSON 可读

如需更高级的功能，可以查看官方文档中的 Formatters、Resolvers 和自定义序列化器等主题。