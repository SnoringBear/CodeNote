# GCC

GCC（GNU Compiler Collection）是一套由GNU项目开发的编译器系统，支持多种编程语言，包括C、C++、Objective-C、Fortran、Ada、Go和D等。GCC最初是作为C语言的编译器（GNU C Compiler）开发的，后来扩展为支持多种语言的编译器集合。

### 主要特点

1. **跨平台支持**：GCC可以在多种操作系统和硬件架构上运行，包括Linux、Windows、macOS等。
2. **开源**：GCC是自由软件，遵循GNU通用公共许可证（GPL），用户可以自由使用、修改和分发。
3. **优化能力强**：GCC提供了多种优化选项，能够生成高效的机器代码。
4. **多语言支持**：除了C和C++，GCC还支持多种其他编程语言。
5. **插件支持**：GCC支持插件，允许开发者扩展编译器的功能。

### 常用命令

- **编译C程序**：

  

  ```bash
  gcc -o output_file input_file.c
  ```

- **编译C++程序**：

  

  ```bash
  g++ -o output_file input_file.cpp
  ```

- **启用优化**：

  

  ```bash
  gcc -O2 -o output_file input_file.c
  ```

- **生成调试信息**：

  

  ```bash
  gcc -g -o output_file input_file.c
  ```

- **显示所有警告**：

  

  ```bash
  gcc -Wall -o output_file input_file.c
  ```

### 常用选项

- `-o`：指定输出文件名。
- `-O`：优化级别（如`-O0`、`-O1`、`-O2`、`-O3`）。
- `-g`：生成调试信息。
- `-Wall`：启用所有警告。
- `-I`：指定头文件目录。
- `-L`：指定库文件目录。
- `-l`：链接指定的库。

### 示例

假设有一个C程序`hello.c`：



```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

可以使用以下命令编译并运行：



```bash
gcc -o hello hello.c
./hello
```

### 相关工具

- **g++**：用于编译C++程序。
- **gdb**：GNU调试器，用于调试程序。
- **make**：自动化构建工具，通常与GCC一起使用来管理复杂的编译过程。

GCC是开发C和C++程序的重要工具，广泛应用于开源项目和商业软件开发中