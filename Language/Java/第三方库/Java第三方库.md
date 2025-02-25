## 一、Quartz





## 二、Apache Commons Lang

#### 1、StopWatch

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

// 运行结果
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

**对StopWatch类使用的思考**

由于start()和stop()方法通常需要成对被调用，而且通常分别处于被计时代码块的起始位置和结束位置，所以一定要注意被计时的代码块中的逻辑是否有可能导致stop()和start()的成对调用关系被破坏而抛出IllegalStateException(如，tryCatchFinally,continue,break等等)



