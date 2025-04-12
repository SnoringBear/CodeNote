**Scriban** 是一个用于 .NET 平台的轻量级模板引擎，支持高效的文本生成和模板渲染。它的设计理念是简洁、快速和强大，适用于生成 HTML、JSON、XML、SQL 查询等各种文本内容。

Scriban 的语法灵感来自于 **Mustache** 和 **Liquid** 模板引擎，但相比于这些模板引擎，Scriban 提供了更多的功能和更高的性能，特别是在处理复杂逻辑时。

### Scriban 的主要特性：

1. **高效的渲染**：Scriban 的渲染速度非常快，适合用在高并发的应用中。
2. **灵活的语法**：支持变量替换、控制结构（如 if、for、while）、过滤器和自定义函数等。
3. **易于嵌入**：可以非常容易地嵌入到 .NET 应用程序中，无论是 Web 应用、桌面应用还是其他类型的应用。
4. **支持表达式**：支持复杂的表达式和运算，可以在模板中直接执行逻辑运算。
5. **可扩展性**：支持自定义函数、过滤器和宏，提供了良好的扩展点。

### 安装 Scriban

你可以通过 NuGet 安装 Scriban：

```bash
dotnet add package Scriban
```

或者在 `*.csproj` 文件中添加以下依赖：

```xml
<PackageReference Include="Scriban" Version="4.0.0" />
```

### 使用 Scriban

以下是一些基本的示例，展示如何在 C# 中使用 Scriban 来渲染模板。

#### 1. 基本模板渲染

假设你有一个简单的模板，需要渲染某些变量：

```csharp
using Scriban;

public class Program
{
    public static void Main()
    {
        // 定义模板
        var template = "Hello, {{ name }}! Today is {{ day }}.";

        // 创建一个字典作为数据上下文
        var context = new { name = "Alice", day = "Monday" };

        // 渲染模板
        var result = Template.Parse(template).Render(context);

        Console.WriteLine(result);  // 输出: Hello, Alice! Today is Monday.
    }
}
```

在这个例子中，`{{ name }}` 和 `{{ day }}` 是模板中的占位符，它们会被相应的值替换。



#### 2. 使用条件语句（`if`）

Scriban 支持条件语句，可以在模板中根据数据动态改变渲染内容：

```csharp
using Scriban;

public class Program
{
    public static void Main()
    {
        var template = "Hello, {{ name }}! {% if age >= 18 %}You are an adult.{% else %}You are a minor.{% endif %}";
        
        var context = new { name = "Bob", age = 20 };

        var result = Template.Parse(template).Render(context);

        Console.WriteLine(result);  // 输出: Hello, Bob! You are an adult.
    }
}
```

在这个例子中，`{% if age >= 18 %}` 是一个条件判断语句，它根据 `age` 的值渲染不同的内容。



#### 3. 使用循环语句（`for`）

Scriban 也支持 `for` 循环，能够遍历数据并生成多次内容：

```csharp
using Scriban;
using System;

public class Program
{
    public static void Main()
    {
        var template = "{% for item in items %}Item: {{ item }}{% endfor %}";

        var context = new { items = new[] { "Apple", "Banana", "Cherry" } };

        var result = Template.Parse(template).Render(context);

        Console.WriteLine(result);  
        // 输出: Item: AppleItem: BananaItem: Cherry
    }
}
```

在这个例子中，`{% for item in items %}` 会遍历 `items` 数组，生成每个元素的输出。



#### 4. 使用过滤器

Scriban 提供了内置的过滤器，可以对变量进行格式化操作。例如，`uppercase` 过滤器将字符串转换为大写：

```csharp
using Scriban;

public class Program
{
    public static void Main()
    {
        var template = "Hello, {{ name | uppercase }}!";

        var context = new { name = "alice" };

        var result = Template.Parse(template).Render(context);

        Console.WriteLine(result);  // 输出: Hello, ALICE!
    }
}
```



#### 5. 自定义函数

Scriban 允许你在模板中使用自定义函数。你可以将 C# 函数暴露到模板中进行调用：

```csharp
using Scriban;

public class Program
{
    public static string Greet(string name)
    {
        return $"Hello, {name}!";
    }

    public static void Main()
    {
        var template = "{{ greet('Alice') }}";

        // 创建一个模板上下文并添加自定义函数
        var context = new { greet = (Func<string, string>) Greet };

        var result = Template.Parse(template).Render(context);

        Console.WriteLine(result);  // 输出: Hello, Alice!
    }
}
```



#### 6. 渲染到文件

你还可以将渲染结果保存到文件中，例如生成 HTML 文件：

```csharp
using Scriban;
using System.IO;

public class Program
{
    public static void Main()
    {
        var template = "<h1>Hello, {{ name }}</h1>";

        var context = new { name = "Alice" };

        // 渲染模板
        var result = Template.Parse(template).Render(context);

        // 将渲染结果保存到文件
        File.WriteAllText("output.html", result);
    }
}
```



### 总结

Scriban 是一个强大且灵活的模板引擎，非常适合用来生成各种文本文件，例如 HTML、JSON、SQL 等。它提供了丰富的功能，包括条件判断、循环、过滤器、函数扩展等，同时保持了简单和高效的设计。无论是简单的模板渲染还是复杂的文本生成，Scriban 都能高效完成。

如果你有更具体的需求或疑问，可以随时告诉我，我会帮助你解决！