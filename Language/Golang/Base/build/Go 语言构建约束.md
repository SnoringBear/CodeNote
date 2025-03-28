# Go 语言的构建约束



构建约束（Build Constraints）是 Go 语言中用于控制代码文件在特定条件下是否参与编译的机制。它允许开发者根据不同的操作系统、架构、编译环境等因素来选择性编译代码。



### 1. 文件注释形式

1. 构建约束注释必须出现在文件开头，`package` 声明之前
2. 注释后必须有一个空行
3. 逻辑表达式从左到右计算，不支持括号改变优先级

```go
// +build linux,amd64

package main
```



### 2. 文件命名形式

1. 当使用文件名作为构建约束时，必须使用下划线连接

```bash
mypkg_linux_amd64.go
mypkg_windows.go
mypkg_linux.go        	// 仅Linux
mypkg_windows_amd64.go 	// 仅Windows amd64
mypkg_!plan9.go       	// 非Plan9系统
mypkg.go          		// 默认实现
```



### 3. 使用自定义标签编译：

```bash
go build -tags debug,experimental
go test -tags integration
```

