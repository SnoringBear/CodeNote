# Go语言 ``http``  里面的body不关闭导致那个对象内存泄漏

在Go语言中，如果HTTP请求的`body`没有关闭，可能会导致特定对象的内存泄漏。具体来说，未关闭HTTP响应体（`resp.Body`）会导致一些底层资源无法释放，从而导致内存泄漏或其他资源（如文件描述符、网络连接）被占用。

### 主要受影响的对象

1. **底层TCP连接（`net.Conn`）**
   - HTTP客户端使用TCP连接与服务器通信。HTTP响应的`Body`通常是一个流（基于TCP连接）。如果不关闭`resp.Body`，这个TCP连接将无法释放，导致连接被占用，无法被复用或关闭。
   - 在使用HTTP连接池时，未关闭的连接会保留在内存中，导致连接池的资源耗尽。
2. **HTTP传输（`http.Transport`）**
   - Go语言的`http.Client`底层使用`http.Transport`管理HTTP连接和请求。未关闭的`resp.Body`可能会阻止`http.Transport`释放与此请求关联的内存和资源。如果长期不释放，内存和系统资源将持续增长。
   - `http.Transport`会管理连接池，如果连接未关闭，将导致连接池资源泄漏，进而影响整个客户端的并发性能。
3. **缓冲区（`bufio.Reader`）**
   - HTTP响应的内容通常通过`bufio.Reader`读取。如果响应体未关闭，`bufio.Reader`中可能还有未读取的数据，导致这些缓冲区未释放并留在内存中。
4. **文件描述符**
   - 底层的网络连接或文件句柄未被关闭时，会保持打开状态。这些文件描述符是系统资源，如果长期不关闭，系统可用的文件描述符会耗尽，影响整个系统或应用程序的正常运行。

### 示例代码分析

假设我们发送一个HTTP请求并获取响应，但忘记关闭`resp.Body`，这会导致资源泄漏：

```go
package main

import (
    "log"
    "net/http"
)

func fetchURL() {
    resp, err := http.Get("http://example.com")
    if err != nil {
        log.Fatal(err)
    }

    // 忘记关闭 resp.Body 可能导致资源泄漏
    // defer resp.Body.Close() 应该在此处被调用
}
```

### 影响总结

如果不调用`resp.Body.Close()`，以下资源将无法释放：

1. **TCP连接**: 底层的TCP连接无法关闭，可能会导致连接池资源耗尽，并且系统无法复用这些连接。
2. **内存泄漏**: 与HTTP请求和响应相关的内存对象（如缓冲区、数据流）会在内存中持续存在，无法被垃圾回收，导致内存泄漏。
3. **文件描述符泄漏**: 每个打开的连接都会占用系统文件描述符，如果不关闭，可能会耗尽系统的文件描述符限制，从而影响其他进程。

### 正确做法

在处理HTTP请求时，确保始终关闭响应体以释放相关资源。最佳实践是使用`defer`语句：

```go
package main

import (
    "io/ioutil"
    "log"
    "net/http"
)

func fetchURL() {
    resp, err := http.Get("http://example.com")
    if err != nil {
        log.Fatal(err)
    }
    defer resp.Body.Close() // 确保响应体总是被关闭

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        log.Fatal(err)
    }
    log.Println(string(body))
}
```

这样可以确保即使函数中发生错误，`resp.Body`也会被正确关闭，防止资源泄漏。

### 结论

如果不关闭Go语言中HTTP请求的`resp.Body`，底层的TCP连接、文件描述符、缓冲区等资源无法被正确释放，导致资源泄漏。这不仅会影响程序的内存使用，还可能导致连接池资源耗尽，影响系统性能。