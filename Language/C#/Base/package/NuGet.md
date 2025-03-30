NuGet 是 **.NET 平台**的官方包管理器（类似其他语言中的 npm、pip、Maven），用于管理项目中的依赖项（第三方库、工具或框架）。以下是关键点：

------

### **核心功能**

1. **依赖管理**
   - 自动下载、安装、更新或卸载库（如 `Newtonsoft.Json`、`EntityFramework`）。
   - 解决依赖冲突，确保版本兼容性。
2. **包来源**
   - 默认从官方仓库 [nuget.org](https://www.nuget.org/) 获取包，也支持私有仓库（如 Azure Artifacts、本地文件夹）。
3. **集成开发环境（IDE）支持**
   - 与 Visual Studio 深度集成，提供图形化界面管理包。
   - 命令行工具 `nuget.exe` 或 `dotnet CLI`（如 `dotnet add package`）支持自动化操作。

------

### **使用场景**

- **添加库**：例如，为项目添加日志库 `Serilog`：

  bash

  复制

  ```
  dotnet add package Serilog
  ```

- **恢复依赖**：通过 `nuget restore` 或 `dotnet restore` 还原项目所需的所有包。

- **发布包**：将自研代码打包（`.nupkg` 文件）并发布到 NuGet 仓库供他人使用。

------

### **配置文件**

- **`packages.config`**（旧项目）或 **`<PackageReference>`**（新项目）记录依赖项：

  xml

  复制

  ```
  <!-- .csproj 文件示例 -->
  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore" Version="7.0.0" />
  </ItemGroup>
  ```

  运行 HTML

------

### **优势**

- **节省时间**：避免手动下载和配置库。
- **版本控制**：支持语义化版本（SemVer），灵活指定版本范围。
- **生态丰富**：提供超过 30 万个包（如 ML.NET、Dapper、xUnit）。

------

### **常见命令**

| 命令                        | 作用         |
| :-------------------------- | :----------- |
| `dotnet add package <包名>` | 安装包       |
| `dotnet list package`       | 查看已安装包 |
| `nuget push <包路径>`       | 发布包到仓库 |

------

如果你使用 Visual Studio，可以通过右键项目 → **“管理 NuGet 程序包”** 可视化操作。对于问题排查，可检查 `nuget.config` 文件配置的包源。