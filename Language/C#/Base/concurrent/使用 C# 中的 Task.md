# 使用 C# 中的 Task

Task 是 C# 中用于表示异步操作的核心类，它是基于任务的异步模式 (TAP) 的基础。以下是 Task 的详细使用方法：

## 基本用法

### 创建和启动 Task

```csharp
// 方式1: 使用 Task.Run (最常用)
Task task1 = Task.Run(() => 
{
    Console.WriteLine("Task 1 正在运行");
});

// 方式2: 使用 Task.Factory.StartNew
Task task2 = Task.Factory.StartNew(() => 
{
    Console.WriteLine("Task 2 正在运行");
});

// 方式3: 直接实例化并启动
var task3 = new Task(() => Console.WriteLine("Task 3 正在运行"));
task3.Start();
```



### 等待 Task 完成

```csharp
task1.Wait(); // 阻塞当前线程直到任务完成
Task.WaitAll(task1, task2); // 等待多个任务完成
Task.WaitAny(task1, task2); // 等待任意一个任务完成
```



## 返回值 Task<TResult>

```csharp
Task<int> calculateTask = Task.Run(() => 
{
    Thread.Sleep(1000); // 模拟耗时操作
    return 42;
});

// 获取结果(会阻塞直到任务完成)
int result = calculateTask.Result;

// 更好的方式是使用 await
int result = await calculateTask;
```



## 异步方法 (async/await)



```csharp
public async Task DoWorkAsync()
{
    Console.WriteLine("开始工作");
    
    await Task.Delay(1000); // 异步等待
    
    Console.WriteLine("工作完成");
}

// 调用
await DoWorkAsync();
```



## 异常处理



```csharp
try
{
    await TaskThatMightFail();
}
catch (Exception ex)
{
    Console.WriteLine($"发生错误: {ex.Message}");
}

async Task TaskThatMightFail()
{
    await Task.Delay(100);
    throw new InvalidOperationException("模拟错误");
}
```



## 任务组合



```csharp
// 顺序执行
var result1 = await Task1();
var result2 = await Task2(result1);

// 并行执行
var task1 = Task1();
var task2 = Task2();
await Task.WhenAll(task1, task2);

// 选择第一个完成的任务
var completedTask = await Task.WhenAny(task1, task2);
```



## 取消任务



```csharp
var cts = new CancellationTokenSource();

// 启动可取消的任务
var task = Task.Run(() => 
{
    while (true)
    {
        cts.Token.ThrowIfCancellationRequested();
        // 工作代码
    }
}, cts.Token);

// 取消任务
cts.CancelAfter(2000);

try
{
    await task;
}
catch (OperationCanceledException)
{
    Console.WriteLine("任务已取消");
}
```



## 高级用法



### 任务延续

```csharp
Task.Run(() => Console.WriteLine("第一个任务"))
    .ContinueWith(t => Console.WriteLine("延续任务1"))
    .ContinueWith(t => Console.WriteLine("延续任务2"));
```



### 配置延续选项

```csharp
var task = Task.Run(() => { /* ... */ });

task.ContinueWith(t => 
{
    // 只在任务失败时执行
}, TaskContinuationOptions.OnlyOnFaulted);
```



### 使用 TaskCompletionSource

```csharp
var tcs = new TaskCompletionSource<int>();

// 在其他地方完成这个任务
tcs.SetResult(42); // 或 SetException, SetCanceled

int result = await tcs.Task;
```



## 最佳实践

1. 尽量使用 `async/await` 而不是直接使用 `Task.Result` 或 `Task.Wait()`
2. 避免 `async void` 方法，除非是事件处理程序
3. 考虑使用 `ConfigureAwait(false)` 在库代码中避免死锁
4. 合理使用 `CancellationToken` 实现取消功能
5. 注意异常处理，未观察的异常可能导致应用程序崩溃

这些是 C# 中 Task 的基本和高级用法，合理使用可以编写出高效、响应性好的异步代码。