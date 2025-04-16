`Source Generators` 是 C# 9 / .NET 5 引入的一项强大功能，属于编译期元编程的一部分。它允许你在 **编译时动态生成 C# 源代码**，并将这些代码无缝地加入到项目中。

------

## 🧠 什么是 Source Generator？

简而言之：**它是一段在编译期间运行的代码，会“生成”新的 C# 代码加入到你的项目中。**

和运行时反射不同，它在编译期就完成了分析和代码生成，因此：

- ✅ 性能更高（没有运行时反射）
- ✅ 更安全（类型检查在编译期完成）
- ✅ 更智能（IDE 支持，如 IntelliSense）

------

## 🛠 常见用途示例



| 应用场景           | 示例                                                         |
| ------------------ | ------------------------------------------------------------ |
| 自动生成序列化代码 | `System.Text.Json` source generator                          |
| 绑定配置到 POCO 类 | `Microsoft.Extensions.Configuration.Binder`                  |
| 自动实现通知属性   | `INotifyPropertyChanged` 的自动生成                          |
| 依赖注入注册       | 如 `MediatR.Extensions.Microsoft.DependencyInjection.Generators` |
| 映射对象           | `Mapster` source generator、`AutoMapper`（实验中）           |

------

## 📁 Visual Studio 中显示在哪？

在 `Dependencies > Source Generators` 下，你会看到引入的所有 Source Generator（一般是从 NuGet 包中来）。

比如：

```csharp
pgsqlCopyEditDependencies
├── Source Generators
│   ├── Microsoft.Extensions.Logging.Generators
│   └── System.Text.Json.SourceGeneration
```

------

## 🧬 原理简要

一个 Source Generator 是一个实现了接口 `ISourceGenerator` 的类：

```csharp
[Generator]
public class MyGenerator : ISourceGenerator
{
    public void Initialize(GeneratorInitializationContext context)
    {
        // 注册语法接收器等
    }

    public void Execute(GeneratorExecutionContext context)
    {
        // 动态生成代码
        context.AddSource("MyGeneratedCode", SourceText.From(...));
    }
}
```

------

## ⚠ 注意事项

- 🔄 生成代码是**增量的**，不会修改你已有的源码。
- 🔐 不建议用于生成带业务逻辑的复杂代码，适合生成样板代码（boilerplate）。
- 🔧 需要 NuGet 包支持 Source Generator，例如 `.g.cs` 文件通常自动添加进项目。