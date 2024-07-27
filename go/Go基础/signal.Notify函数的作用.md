# signal.Notify函数的作用

signal.Notify 是Go标准库 os/signal 包中的一个函数，用于注册信号监听器

### 一、常用信号类型

* syscall.SIGHUP 是挂断信号（Hangup），通常在终端断开连接时发送给进程
* syscall.SIGQUIT 是退出请求信号（Quit），通常由用户按下 Ctrl+\ 发送，生成核心转储后退出进程
* syscall.SIGTERM 是终止信号（Termination），温和地请求进程终止，可以被捕获和处理
* syscall.SIGINT 是中断信号（Interrupt），通常由用户按下 Ctrl+C 发送，也请求进程终止。



### 二、代码示例

```go

c := make(chan os.Signal, 1)
signal.Notify(c, syscall.SIGHUP, syscall.SIGQUIT, syscall.SIGTERM, syscall.SIGINT)
// 这段代码的作用是设置一个信号处理器，当进程接收到 SIGHUP, SIGQUIT, SIGTERM 或 SIGINT 信号中的任何一个时，它会在 c 通道上发送一个信号值。
// 这样，你的程序就可以通过监听 c 通道来知道何时发生了这些事件，并可以执行相应的清理工作，比如关闭打开的文件、停止服务、保存状态等，实现优雅的退出。

// 当有信号到达时，这个循环会停止
go func() {
    sig := <-c
    fmt.Printf("Received signal: %s\n", sig)
    // 这里执行清理工作
    cleanup()
    os.Exit(0)
}()

// 你的主要业务逻辑在这里
mainLogic()

```





