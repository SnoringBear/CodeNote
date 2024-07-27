在 IntelliJ IDEA 中有两个 Mac 版本的快捷键，一个叫做：Mac OS X，一个叫做：Mac OS X 10.5+，目前都是用：**Mac OS X 10.5+**。

### 编辑

- Command + Shift + Enter 自动结束代码，行末自动添加分号
- Command + P 显示方法的参数信息
- Control + J 快速查看文档
- Command + N 生成代码（getter、setter、构造函数、hashCode/equals,toString）
- Control + O 覆盖方法（重写父类方法）
- Control + I 实现方法（实现接口中的方法）
- Command + / 注释/取消注释与行注释
- Command + Option + / 注释/取消注释与块注释
- Option + 方向键上 连续选中代码块
- Option + 方向键下 减少当前选中的代码块
- Control + Shift + Q 显示上下文信息
- Option + Enter 显示意向动作和快速修复代码
- Command + Option + L 格式化代码
- Control + Option + O 优化import
- Command + Delete 删除当前行或选定的块的行
- Shift + Enter 开始新的一行
- Tab / Shift + Tab 缩进代码 / 反缩进代码
- Command + 加号 / Command + 减号 展开 / 折叠代码块
- Command + Shift + 加号 展开所以代码块
- Command + Shift + 减号 折叠所有代码块

### 查询/替换

- Double Shift 查询任何东西
- Command + Shift + F 全局查找（根据路径）

### 使用查询

- Command + Option + F7 显示用法

### 编译和运行

- Command + F9 编译Project
- Command + Shift + F9 编译选择的文件、包或模块
- Control + Option + R 弹出 Run 的可选择菜单
- Control + Option + D 弹出 Debug 的可选择菜单
- Control + R 运行
- Control + D 调试

### 调试

- F8 进入下一步，如果当前行断点是一个方法，则不进入当前方法体内
- F7 进入下一步，如果当前行断点是一个方法，则进入当前方法体内，如果该方法体还有方法，则不会进入该内嵌的方法中
- Shift + F7 智能步入，断点所在行上有多个方法调用，会弹出进入哪个方法
- Shift + F8 跳出
- Option + F9 运行到光标处，如果光标前有其他断点会进入到该断点
- Option + F8 计算表达式（可以更改变量值使其生效）
- Command + Option + R 恢复程序运行，如果该断点下面代码还有断点则停在下一个断点上
- Command + F8 切换断点（若光标当前行有断点则取消断点，没有则加上断点）

### 导航

- Command + E 显示最近打开的文件记录列表
- Option + 方向键左 / Option + 方向键右 光标跳转到当前单词 / 中文句的左 / 右侧开头位置
- Command + Option + 方向键左 / Command + Option + 方向键右 退回 / 前进到上一个操作的地方
- Command + B / Command + 鼠标点击 进入光标所在的方法/变量的接口或是定义处
- Command + Option + B 跳转到实现处，在某个调用的方法名上使用会跳到具体的实现处，可以跳过接口
- Command + Y 快速打开光标所在方法、类的定义
- Control + Shift + B 在光标所在变量上跳转到类型声明处
- Command + U 前往当前光标所在方法的父类的方法 / 接口定义
- Command + F12 弹出当前文件结构层，可以在弹出的层上直接输入进行筛选（可用于搜索类中的方法）

### 重构

- F5 复制文件到指定目录
- F6 移动文件到指定目录
- Shift + F6 重命名类/文件/变量/函数等
- Command + Option + M 将选中的代码提取为方法
- Command + Option + V 提取变量
- Command + Option + F 提取字段
- Command + Option + C 提取常量
- Command + Option + P 提取参数

### 版本控制/本地历史记录

- Command + K 提交代码到版本控制器
- Command + T 从版本控制器更新代码

### 通用

- Command + , 打开IDEA系统设置
- Command + ; 打开项目结构对话框
