

### 一 、在Visual Studio Code（VSCode）中将 Git Bash 设置为默认终端

你可以按照以下步骤进行：

1. 打开 VSCode。
2. 打开命令面板：使用快捷键 `Ctrl + Shift + P`（Windows/Linux）或 `Cmd + Shift + P`（Mac）。
3. 在命令面板中输入 "Select Default Shell" 并选择 "Terminal: Select Default Shell"。
4. 选择 "Git Bash" 作为默认终端。
5. 重新启动 VSCode。

现在，VSCode 将使用 Git Bash 作为默认终端。当你打开终端时，它将自动启动 Git Bash。



### 二、安装node或者其他第三方模块的声明文件

在TypeScript中，声明文件（`.d.ts` 文件）用于描述 JavaScript 模块或库的类型信息，以便在使用TypeScript时能够获得代码补全和类型检查的支持。

对于Node.js，你可以通过以下步骤安装官方的声明文件：



1、**初始化 TypeScript 项目：** 如果你的项目还没有 `tsconfig.json` 文件，你可以通过以下命令初始化 TypeScript 项目：

```bash
tsc --init
```



2、 **安装 `@types/node` 声明文件：** 使用 npm 安装 Node.js 的声明文件包：

```bash
npm install --save-dev @types/node@version

// @version 为当前使用node的版本号
```

这会将 `@types/node` 包添加到你的项目的 `devDependencies` 中，并提供 Node.js 的类型定义。



3、**使用声明文件：** TypeScript 会自动查找 `node_modules/@types` 目录下的声明文件。因此，你不需要额外的配置，只要安装了 `@types/node` 包，TypeScript 将自动使用它。

这样，你的 TypeScript 项目就能够使用 Node.js 的类型定义了。确保在代码中使用正确的导入语句，例如：

```bash
import * as fs from 'fs';
import * as path from 'path';

// 现在 fs 和 path 对象的类型信息将受到 TypeScript 的支持。

```

请注意，从 TypeScript 2.0 版本开始，大多数主要的 Node.js 类型定义都被整合到 `@types/node` 中，因此直接安装这个包通常就足够了