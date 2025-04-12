`FodyWeavers.xml` 是用于配置 [.NET](https://dotnet.microsoft.com/) 项目中 [Fody](https://github.com/Fody/Fody) 插件的核心文件。Fody 是一个 IL 编织（IL Weaving）工具，允许在编译后自动修改 IL（中间语言）代码，比如自动实现 `INotifyPropertyChanged` 接口等。

------

### 示例：FodyWeavers.xml 文件内容

这是一个典型的 `FodyWeavers.xml` 示例：

```
xmlCopyEdit<?xml version="1.0" encoding="utf-8" ?>
<Weavers>
  <PropertyChanged />
  <Costura />
</Weavers>
```

------

### 配置说明：

- `<Weavers>`：根节点，包含所有启用的插件。
- `<PropertyChanged />`：启用 [PropertyChanged.Fody](https://github.com/Fody/PropertyChanged) 插件，用于自动实现 `INotifyPropertyChanged`。
- `<Costura />`：启用 [Costura.Fody](https://github.com/Fody/Costura) 插件，用于将所有引用的 DLL 嵌入主程序，变成一个单文件发布。

------

### 插件可以带参数：

```
xmlCopyEdit<Weavers>
  <PropertyChanged>
    <InjectOnPropertyName>true</InjectOnPropertyName>
  </PropertyChanged>
</Weavers>
```

------

### 使用位置：

这个文件一般位于项目根目录，和 `.csproj` 同级。MSBuild 和 Fody 会自动读取它进行编译后处理。

------

### 常见插件列表（可组合使用）：

| 插件名称             | 功能简介                        |
| -------------------- | ------------------------------- |
| PropertyChanged.Fody | 自动实现 INotifyPropertyChanged |
| Costura.Fody         | 嵌入依赖 DLL                    |
| MethodTimer.Fody     | 方法执行时间记录                |
| NullGuard.Fody       | 自动空值检查                    |
| Anotar.Fody          | 日志注入                        |
| Validar.Fody         | 参数验证                        |

------

如果你使用 `NuGet` 安装某个插件，它一般会自动添加 `<Weavers>` 节点到此 XML 文件中。

------

需要根据你的项目需求添加不同的插件，如果你告诉我你需要实现什么功能，我可以帮你写对应的配置。