**业务代码**或者**算法**的**性能**一直是我们重要的关注点之一。而衡量性能的重要指标之一就是时间复杂度。我们除了用大O表示法外，有时还需要直观地通过程序的实际运行时间衡量算法的性能。今天我们就介绍一下在计时方面十分重要的工具类**StopWatch**。

StopWatch是不在java标准包中的，你可以在以下两个程序包中找到StopWatch工具类

- **org.apache.commons.lang3.time** 
   - Apache Commons Lang提供了一些Java标准库中没有提供的类与方法，尤其是在String操作方法，基础数值方法，对象引用，并发行，创建及序列化，系统属性等方面提供了强大的功能
- **org.springframework.util** 
   - spring框架为现代java企业级应用提供了一个简洁并且富有表达能力的可以运行在任何部署平台的编程与设置模型

现在分别讲解一下两程序包中的StopWatch工具类的具体用法。

### **Apache Commons Lang中的StopWatch**

Apache Commons Lang中的StopWatch与我们平时常用的秒表的行为比较类似，我们先看一下其中的一些重要方法

- 构造器：可以使用无参构造器，也可以使用传入了一个String类型的message的构造器，创建一个有字符串表示的秒表对象
- 方法 
   - start()：开始计时
   - stop()：停止计时
   - suspend()：暂停秒表
   - resume()：恢复秒表
   - reset()：重置秒表
   - split()：设定split点
   - getSplitTime()：获取时间，时间为从计时起点到最新设置的split点之间的时长
   - getTime()：显示当前秒表时间，可以通过指定TimeUnit来指定返回的时间单位

**附示例代码（Junit测试类）**

```java
import org.apache.commons.lang3.time.StopWatch;
import org.junit.Test;

import java.util.concurrent.TimeUnit;

public class StopWatchTest {
    @Test
    public void testStopWatch() throws InterruptedException {
        StopWatch stopWatch = new StopWatch();
        sleepOneSecondAndGetStopWatchReading(stopWatch);
        stopWatch.start();
        sleepOneSecondAndGetStopWatchReading(stopWatch);
        stopWatch.stop();
        sleepOneSecondAndGetStopWatchReading(stopWatch);
        stopWatch.reset();
        sleepOneSecondAndGetStopWatchReading(stopWatch);
        stopWatch.start();
        sleepOneSecondAndGetStopWatchReading(stopWatch);
        stopWatch.split();
        sleepOneSecondAndGetStopWatchReading(stopWatch);
        stopWatch.split();
        sleepOneSecondAndGetStopWatchReading(stopWatch);
        stopWatch.unsplit();
        sleepOneSecondAndGetStopWatchReading(stopWatch);
        stopWatch.split();
        System.out.println("Last Split time :"+stopWatch.getSplitTime()+" ms");
        System.out.println(stopWatch.toSplitString());
        System.out.println(stopWatch.toString());
    }

    private void sleepOneSecondAndGetStopWatchReading(StopWatch stopWatch) throws InterruptedException {
        Thread.sleep(1000);
        System.out.println("StopWatch Time: "+stopWatch.getTime(TimeUnit.MILLISECONDS)+"ms");
    }
}
```

### **运行结果**

```java
StopWatch Time**:** 0ms
StopWatch Time**:** 1013ms
StopWatch Time**:** 1013ms
StopWatch Time**:** 0ms
StopWatch Time**:** 1006ms
StopWatch Time**:** 2009ms
StopWatch Time**:** 3024ms
StopWatch Time**:** 4031ms
Last Split time **:**4031 ms
00:00**:**04**.**031
00:00**:**04**.**049

Process finished with exit code 0
```

### **Spring Framework中的StopWatch**

相比于Apache Commons Lang中的StopWatch，Spring Framework中的StopWatch提供了额外的任务计时功能：不仅可以统计单个任务时间，还可以优雅地打印出多个任务的详细耗时情况

我们先看一下StopWatch类的构造器和一些关键方法

- 构造器：可以使用无参数构造器，也可使用传入一个String类型的id，创建一个指定了id的StopWatch
- 方法 
   - start()：可以不传入参数，开始一个**无名称的任务**的计时，也可以传入String类型的参数来开始指定任务名的任务计时
   - stop()：停止当前任务的计时
   - isRunning()：返回此stopWatch是否正在计时某任务
   - getTotalTimeMillis()：返回所有任务的总体执行时间(毫秒单位)
   - getLastTaskTimeMillis()：返回上一个任务的耗时(毫秒单位)
   - prettyPrint()：优美地打印所有任务的详细耗时情况

### **示例代码**

```java
public static void main(String[] args) {
    StopWatch stopWatch = new StopWatch();
    for (int i=0;i<10;i++){
        stopWatch.start("Task"+i);
        //Some Real Business Logic
        stopWatch.stop();
    }
    stopWatch.prettyPrint();
}
```

代码中启动并在真正的业务逻辑完成后分别停止了十个任务的计时，最终我们可以通过prettyPrint方法来获得详细的任务执行耗时情况

### **对StopWatch类使用的思考**

由于start()和stop()方法通常需要成对被调用，而且通常分别处于被计时代码块的起始位置和结束位置，所以一定要注意被计时的代码块中的逻辑是否有可能导致stop()和start()的成对调用关系被破坏而抛出IllegalStateException(如，tryCatchFinally,continue,break等等)
