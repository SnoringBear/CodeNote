**Quartz.NET** 是一个功能强大的作业调度库，用于在 .NET 应用程序中执行计划任务。它允许你创建、调度、执行和管理任务（或称作“作业”），这些任务可以根据指定的时间表周期性地执行。Quartz.NET 提供了丰富的调度功能，包括按时间间隔、日期、日历、Cron 表达式等多种方式进行调度。



### Quartz.NET 的主要特性

1. **作业调度**：可以通过 Quartz 定义并调度任务（Job）。任务可以是一次性执行的，也可以是周期性执行的。
2. **灵活的时间表**：支持使用 Cron 表达式、时间间隔和日期来配置任务的执行时间。
3. **持久化支持**：Quartz 支持将调度的数据持久化到数据库中，这样即使应用程序重新启动，作业依然可以继续执行。
4. **并发执行**：支持并发执行多个作业，并且可以为每个作业配置线程池，优化性能。
5. **作业分组和触发器**：作业和触发器（Trigger）可以进行分组管理，这样可以按组批量调度作业。
6. **支持作业链**：可以将多个作业串联起来，在一个作业执行完毕后执行下一个作业。
7. **作业监听器**：支持自定义监听器，可以在作业执行前、后或失败时执行自定义逻辑。

### 安装 Quartz.NET

你可以通过 NuGet 包管理器安装 Quartz：

```bash
dotnet add package Quartz
```



### 基本用法

以下是一些使用 **Quartz.NET** 的基本步骤，包括作业的定义、调度和执行。



#### 1. 定义作业（Job）

Quartz.NET 中的作业通常实现 `IJob` 接口。作业的核心方法是 `Execute`，你需要在其中编写作业逻辑。

```csharp
using Quartz;
using System;
using System.Threading.Tasks;

public class HelloWorldJob : IJob
{
    public async Task Execute(IJobExecutionContext context)
    {
        Console.WriteLine("Hello, world! The time is: " + DateTime.Now);
    }
}
```

在上面的代码中，我们定义了一个简单的作业，它会在执行时输出当前的时间。



#### 2. 配置和调度作业

接下来，你需要创建调度器，定义作业和触发器，并将它们关联起来。Quartz.NET 提供了 `StdSchedulerFactory` 来创建调度器实例。

```csharp
using Quartz;
using Quartz.Impl;
using System;
using System.Threading.Tasks;

public class Program
{
    public static async Task Main(string[] args)
    {
        // 创建一个调度器
        IScheduler scheduler = await StdSchedulerFactory.GetDefaultScheduler();
        await scheduler.Start();

        // 定义作业
        IJobDetail job = JobBuilder.Create<HelloWorldJob>()
            .WithIdentity("helloWorldJob", "group1") // 定义作业的名称和组
            .Build();

        // 定义触发器，使用Cron表达式设置定时规则（例如，每分钟执行一次）
        ITrigger trigger = TriggerBuilder.Create()
            .WithIdentity("helloWorldTrigger", "group1")
            .StartNow()  // 立即开始
            .WithCronSchedule("0 * * ? * *") // 每分钟执行一次
            .Build();

        // 将作业和触发器调度
        await scheduler.ScheduleJob(job, trigger);

        // 防止程序结束前退出，模拟长时间运行的程序
        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();

        // 关闭调度器
        await scheduler.Shutdown();
    }
}
```

在这个例子中：

- `JobBuilder.Create<HelloWorldJob>()` 用于创建作业。
- `TriggerBuilder.Create()` 用于创建触发器，这里我们使用了一个 Cron 表达式来定义任务的执行频率，`"0 * * ? * *"` 表示每分钟执行一次。
- `scheduler.ScheduleJob(job, trigger)` 将作业和触发器关联起来，调度任务。



#### 3. Cron 表达式

Quartz 支持 **Cron 表达式**，它允许用户精确控制作业执行的时间。Cron 表达式是由六个字段组成，分别表示秒、分钟、小时、日、月和星期：

```
plaintext


CopyEdit
* * * * * *  ←  秒  分钟  小时  日  月  星期
```

常见的 Cron 表达式示例：

- `"0 * * ? * *"`: 每分钟的第 0 秒执行一次
- `"0 0 12 * * ?"`: 每天中午 12 点执行一次
- `"0 15 10 ? * *"`: 每天 10:15 执行一次
- `"0 0/5 14 * * ?"`: 每天下午 2 点至 2:55 每 5 分钟执行一次

更多 Cron 表达式的配置可以参考 Quartz Cron Expressions 文档。



#### 4. 作业持久化

为了保证作业数据在应用程序重启后不丢失，Quartz 可以将作业和触发器的数据持久化到数据库中。Quartz 默认使用内存存储，但你可以通过配置连接数据库来启用持久化。

首先，你需要在 `Quartz` 配置文件中定义数据库连接字符串。一个常见的配置如下：

```xml
<configuration>
  <appSettings>
    <add key="quartz.scheduler.instanceName" value="MyScheduler" />
    <add key="quartz.jobStore.type" value="Quartz.Impl.AdoJobStore.JobStoreTX, Quartz" />
    <add key="quartz.jobStore.driverDelegateType" value="Quartz.Impl.AdoJobStore.StdAdoDelegate, Quartz" />
    <add key="quartz.jobStore.dataSource" value="default" />
    <add key="quartz.jobStore.tablePrefix" value="QRTZ_" />
    <add key="quartz.jobStore.isClustered" value="true" />
    <add key="quartz.dataSource.default.provider" value="SqlServer" />
    <add key="quartz.dataSource.default.connectionString" value="Data Source=your-database-server;Initial Catalog=QuartzDb;Integrated Security=True;" />
  </appSettings>
</configuration>
```

然后在 `Quartz` 配置中启用数据库存储。



#### 5. 作业监听器和作业链

Quartz 支持 **作业监听器**，通过监听作业的执行状态，可以进行作业的监控、错误日志记录等操作。

例如，创建一个监听器，在作业执行后打印信息：

```csharp
public class HelloWorldJobListener : JobListenerSupport
{
    public override string Name => "HelloWorldJobListener";

    public override Task JobWasExecuted(IJobExecutionContext context, JobExecutionException jobException)
    {
        Console.WriteLine("Job executed: " + context.JobDetail.Key);
        return Task.CompletedTask;
    }
}
```

然后将监听器添加到调度器中：

```csharp
scheduler.ListenerManager.AddJobListener(new HelloWorldJobListener(), KeyMatcher<JobKey>.AnyKey());
```

### 总结

**Quartz.NET** 是一个功能强大、灵活且易于集成的作业调度库。它支持丰富的调度策略（如 Cron 表达式、时间间隔等）、持久化存储、并发执行、作业监听和作业链等功能。无论是简单的定时任务还是复杂的任务调度，Quartz 都能够提供高效的解决方案。

如果你有更具体的应用场景或者需要更深入的帮助，请随时告诉我！