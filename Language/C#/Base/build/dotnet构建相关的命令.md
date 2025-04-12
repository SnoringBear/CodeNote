在 .NET 开发中，构建（Build）相关的命令主要用于 **编译代码、管理依赖、打包发布**。以下是完整的 `.NET CLI` 构建相关命令及其用途：

------



## **1. 核心构建命令**

| 命令                 | 作用                                     | 示例                                     |
| :------------------- | :--------------------------------------- | :--------------------------------------- |
| **`dotnet build`**   | 编译项目（默认 Debug 模式）              | `dotnet build -c Release`                |
| **`dotnet publish`** | 生成可部署包（含所有依赖）               | `dotnet publish -c Release -o ./publish` |
| **`dotnet clean`**   | 清理编译输出（`bin/` 和 `obj/`）         | `dotnet clean`                           |
| **`dotnet restore`** | 还原 NuGet 依赖（通常 `build` 自动包含） | `dotnet restore`                         |

------



## **2. 编译选项**

### **（1）配置与输出**

| 参数 | 说明             | 示例                              |                     |
| :--- | :--------------- | :-------------------------------- | ------------------- |
| `-c  | --configuration` | 指定编译模式（`Debug`/`Release`） | `-c Release`        |
| `-o  | --output`        | 自定义输出目录                    | `-o ./build_output` |
| `-f  | --framework`     | 指定目标框架（如 `net8.0`）       | `-f net8.0`         |
| `-r  | --runtime`       | 指定运行时（RID，如 `win-x64`）   | `-r linux-x64`      |

### **（2）高级控制**

| 参数               | 说明                         | 示例                                 |               |
| :----------------- | :--------------------------- | :----------------------------------- | ------------- |
| `--no-restore`     | 跳过自动还原依赖             | `dotnet build --no-restore`          |               |
| `--no-incremental` | 禁用增量编译（强制全量编译） | `dotnet build --no-incremental`      |               |
| `-v                | --verbosity`                 | 设置日志详细度（`quiet`/`detailed`） | `-v detailed` |

------



## **3. 发布（Publish）专用选项**

| 参数                   | 说明                            | 示例                        |
| :--------------------- | :------------------------------ | :-------------------------- |
| `--self-contained`     | 是否包含 .NET 运行时            | `--self-contained true`     |
| `-p:PublishTrimmed`    | 启用剪裁（减少体积）            | `-p:PublishTrimmed=true`    |
| `-p:PublishSingleFile` | 生成单文件可执行程序            | `-p:PublishSingleFile=true` |
| `-p:PublishReadyToRun` | 启用 AOT 预编译（提升启动速度） | `-p:PublishReadyToRun=true` |

------



## **4. 多项目构建**



### **（1）解决方案级操作**

```bash
# 编译解决方案中的所有项目
dotnet build MySolution.sln

# 发布解决方案中的所有项目
dotnet publish MySolution.sln -c Release
```



### **（2）选择性构建**

```bash
# 仅编译指定项目
dotnet build ./src/MyProject/MyProject.csproj
```

------



## **5. 其他实用命令**

| 命令                 | 作用                      | 示例                     |
| :------------------- | :------------------------ | :----------------------- |
| **`dotnet msbuild`** | 直接调用 MSBuild 底层命令 | `dotnet msbuild -t:Pack` |
| **`dotnet pack`**    | 生成 NuGet 包（`.nupkg`） | `dotnet pack -c Release` |
| **`dotnet test`**    | 编译并运行单元测试        | `dotnet test`            |

------



## **6. 典型工作流**

1. **开发阶段**

   ```bash
   dotnet restore
   dotnet build
   dotnet run
   ```

2. **生产部署**

   ```bash
   dotnet publish -c Release -r linux-x64 --self-contained true -o ./publish
   ```

3. **清理构建**

   ```bash
   dotnet clean
   ```

------

## **常见问题**



### **Q1: `build` 和 `publish` 的区别？**

- **`build`**：仅编译代码，输出在 `bin/` 目录，适合开发调试。
- **`publish`**：打包所有依赖（可选运行时），适合部署。



### **Q2: 如何生成最小化容器镜像？**

```bash
dotnet publish -c Release -r linux-x64 \
  --self-contained true \
  -p:PublishTrimmed=true \
  -p:PublishSingleFile=true \
  -o ./publish
```



### **Q3: 如何查看支持的运行时（RID）？**

```bash
dotnet --list-runtimes
```

------

掌握这些命令后，你可以高效管理 .NET 项目的构建、测试和发布流程！