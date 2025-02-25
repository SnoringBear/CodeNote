

`CJS`（CommonJS）和 `ESM`（ECMAScript Modules）是两种不同的模块系统，用于组织和导入 JavaScript 中的模块。

### CommonJS (CJS):

- **用法：** CommonJS 是 Node.js 初始使用的模块系统，它使用 `require()` 函数导入模块，`module.exports` 导出模块。
- **导入：**

```javascript
// 导入模块
const moduleA = require('./moduleA');

// 导入核心模块
const fs = require('fs');
```

- **导出：**

 ```javascript
  // 导出单个值或对象
  module.exports = someValue;
  
  // 导出多个值或对象
  module.exports = {
    func1,
    func2,
    // ...
  };
 ```

### ECMAScript Modules (ESM):

- **用法：** ESM 是 ECMAScript 6（ES6）引入的官方模块系统，它使用 `import` 和 `export` 语句来导入和导出模块。

- **导入：**

```javascript
// 导入默认导出
import moduleA from './moduleA';

// 导入命名导出
import { func1, func2 } from './moduleB';

// 导入核心模块
import fs from 'fs';
```

- **导出：**



 ```javascript
  // 导出默认值
  export default someValue;
  
  // 导出命名值
  export const func1 = () => { /* ... */ };
  export const func2 = () => { /* ... */ };
 ```

### 主要区别：

1. **语法差异：** CommonJS 使用 `require()` 和 `module.exports`，而 ESM 使用 `import` 和 `export`。
2. **加载时机：** CommonJS 是同步加载模块的，而 ESM 是异步加载的，支持动态导入。
3. **作用域：** CommonJS 模块是动态的，模块代码在运行时执行。ESM 模块是静态的，模块代码在解析阶段执行。
4. **导入和导出：** ESM 支持单一的 `default` 导出以及多个命名导出。CommonJS 一般导出一个对象，对象的属性就是导出的内容。

在 Node.js 中，你可以选择使用 CommonJS 或 ESM，具体取决于你的项目需求和 Node.js 版本。从 Node.js 14 开始，Node.js 支持 ESM，但你可能需要配置文件（如 `.mjs` 扩展名或 `"type": "module"` 的 `package.json` 字段）以及考虑到不同模块系统的兼容性问题。