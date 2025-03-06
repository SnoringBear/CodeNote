# Java并发

## 一、锁

### 1、锁的类型

##### 内置锁

* Java语言内置的同步机制，通过关键字synchronized实现

  * synchronized 

    * 作用：

      保证原子性、可见性、有序性

    * 原理：通过JVM底层实现

    * 相似关键字

      * volatile

        * 作用

          不能保证原子性，只能保证可见性、有序性


##### 显式锁

* java.util.concurrent.locks包提供的锁，lock接口的子类。

  * ReentrantReadWriteLock

  * ReentrantLock【可重入锁】

    * 提供的方法

      * void  lock

        加锁，如果锁已经被别人占用了，那就无限等待

      * boolean tryLock(long timeout, TimeUnit unit) 

        尝试获取锁，有一个超时时间

    * 实现原理：AQS




### 2、Java死锁

描述：死锁就是两个或多个进程互相等待对方释放资源，结果大家都动弹不得



##### 死锁产生的条件

```java
public class DeadlockDemo {
    // 创建两个资源
    private static Object Resource1 = new Object();
    private static Object Resource2 = new Object();

    public static void main(String[] args) {
        // 线程1尝试锁定资源1后，再锁定资源2
        new Thread(() -> {
            synchronized (Resource1) {
                System.out.println("线程1锁定资源1");
                try {
                    // 模拟处理资源所需时间
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (Resource2) {
                    System.out.println("线程1锁定资源2");
                }
            }
        }).start();

        // 线程2尝试锁定资源2后，再锁定资源1
        new Thread(() -> {
            synchronized (Resource2) {
                System.out.println("线程2锁定资源2");
                try {
                    // 模拟处理资源所需时间
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (Resource1) {
                    System.out.println("线程2锁定资源1");
                }
            }
        }).start();
    }
}
```



* 互斥条件
  * 指某资源在一段时间内只能被一个进程使用。比如，打印机就是典型的互斥资源，同一时间只能有一个进程使用。
* 占有和等待条件
  * 一个进程至少占有一个资源，同时等待获取其他被其他进程占有的资源。
* 不可剥夺条件
  * 已经分配给一个进程的资源在未使用完之前，不能被剥夺，只能由该进程释放
* 循环等待条件
  * 存在一种进程资源的循环等待链，每个进程占有下一个进程所需的至少一个资源



##### 检测死锁的方法

* 使用工具检测死锁

  * 咱们可以利用一些现成的工具来检测死锁。比如说JConsole和VisualVM、jstack，这两个工具都随JDK提供，使用起来非常方便。
  * 以JConsole为例，只需启动你的Java应用程序，然后打开JConsole，选择你的Java进程，就可以监控线程的状态。如果出现死锁，JConsole会在“线程”标签页显示死锁的信息。

* 代码级别的检测方法

  * 除了使用工具，咱们还可以在代码级别进行检测。Java提供了一些API来帮助咱们实现这一点。举个例子，咱们可以使用ThreadMXBean来检测死锁。

  * 下面是一个使用ThreadMXBean检测死锁的简单例子：

    ```java
    import java.lang.management.ManagementFactory;
    import java.lang.management.ThreadMXBean;
    
    public class DeadlockDetector {
        public static void checkForDeadlocks() {
            ThreadMXBean threadMXBean = ManagementFactory.getThreadMXBean();
            long[] deadlockedThreads = threadMXBean.findDeadlockedThreads();
    
            if (deadlockedThreads != null) {
                System.out.println("检测到死锁，涉及的线程ID如下：");
                for (long threadId : deadlockedThreads) {
                    System.out.println("线程ID: " + threadId);
                }
            } else {
                System.out.println("未检测到死锁。");
            }
        }
    
        public static void main(String[] args) {
            // 这里可以启动你的应用程序或线程
            // ...
    
            // 定期检测死锁
            while (true) {
                try {
                    Thread.sleep(5000); // 每5秒检查一次
                    checkForDeadlocks();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    ```

    



##### 预防死锁

* 破坏死锁的四个必要条件

  * 破坏互斥条件：这个有点难，因为资源本身的特性决定了它是否互斥。比如打印机，就是天生的互斥资源。但咱们可以通过资源复用或者资源池来减少互斥的影响
  * 破坏占有和等待条件：要做到这点，可以让进程在开始执行前一次性申请所有需要的资源。这样就不会在占有一部分资源的情况下等待其他资源了
  * 破坏不可剥夺条件：当一个已经持有资源的进程请求新资源并且不能立即得到时，它必须释放已占有的资源。这样，其他进程就可以使用这些资源，从而避免了死锁
  * 破坏循环等待条件：为系统中的资源定义一个线性的顺序，然后规定每个进程按顺序申请资源。这样就可以避免循环等待的发生。

* 锁定顺序

  * 锁定顺序的原理就是按照一定的顺序申请资源。比如，咱们有资源A和资源B，那就规定所有线程都必须先锁定A再锁定B。这样就可以避免循环等待条件的发生

    ```java
    public class LockOrderDemo {
        private static Object ResourceA = new Object();
        private static Object ResourceB = new Object();
    
        public static void main(String[] args) {
            // 线程1：按照A -> B的顺序加锁
            new Thread(() -> {
                synchronized (ResourceA) {
                    System.out.println("线程1锁定资源A");
    
                    try {
                        // 模拟处理资源所需时间
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
    
                    synchronized (ResourceB) {
                        System.out.println("线程1锁定资源B");
                    }
                }
            }).start();
    
            // 线程2：也按照A -> B的顺序加锁
            new Thread(() -> {
                synchronized (ResourceA) {
                    System.out.println("线程2锁定资源A");
    
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
    
                    synchronized (ResourceB) {
                        System.out.println("线程2锁定资源B");
                    }
                }
            }).start();
        }
    }
    ```

    

* 锁超时

  * 锁超时是另一种策略，它允许线程在等待锁超过一定时间后放弃，从而避免了无限等待的情况。Java中的ReentrantLock支持带超时的锁请求。

    ```java
    import java.util.concurrent.locks.ReentrantLock;
    import java.util.concurrent.TimeUnit;
    
    public class LockTimeoutDemo {
        private static ReentrantLock lock1 = new ReentrantLock();
        private static ReentrantLock lock2 = new ReentrantLock();
    
        public static void main(String[] args) {
            Thread thread1 = new Thread(new LockTask(lock1, lock2), "Thread1");
            Thread thread2 = new Thread(new LockTask(lock2, lock1), "Thread2");
    
            thread1.start();
            thread2.start();
        }
    
        static class LockTask implements Runnable {
            private ReentrantLock firstLock;
            private ReentrantLock secondLock;
    
            public LockTask(ReentrantLock firstLock, ReentrantLock secondLock) {
                this.firstLock = firstLock;
                this.secondLock = secondLock;
            }
    
            @Override
            public void run() {
                try {
                    // 尝试锁定第一个锁，并设置超时
                    if (!firstLock.tryLock(50, TimeUnit.MILLISECONDS)) {
                        System.out.println(Thread.currentThread().getName() + " 无法立即获取锁，放弃并重试");
                        firstLock.lock();
                    }
    
                    // 模拟处理资源所需时间
                    Thread.sleep(100);
    
                    // 尝试锁定第二个锁，并设置超时
                    if (!secondLock.tryLock(50, TimeUnit.MILLISECONDS)) {
                        System.out.println(Thread.currentThread().getName() + " 无法立即获取锁，放弃并重试");
                        secondLock.lock();
                    }
    
                    System.out.println(Thread.currentThread().getName() + " 成功获取两个锁");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    firstLock.unlock();
                    secondLock.unlock();
                }
            }
        }
    }
    ```

    

* 避免不必要的锁

  * 不要过度使用锁，只在必要时加锁

* 使用并发工具包

  * 利用java.util.concurrent等工具包中的并发工具和类



##### 解决死锁的方法

* 识别和定位死锁
  * 通过前面提到的工具，如JConsole，或者代码层面的检测
* 资源重新分配
  * 解决死锁的一个方法是重新分配资源。这意味着在检测到死锁时，可以通过某种方式释放或重新分配资源，从而打破死锁。这种方法可能需要人为干预，比如重启服务或应用程序，但这通常是最后的手段。
* 进程终止
  * 另一种较为极端的方法是终止参与死锁的一个或多个进程。这种方法可以迅速解决死锁，但可能会导致数据丢失或其他副作用。因此，它只适用于无法通过其他方式解决死锁，或者死锁对系统影响极大时的情况。





## 二、标准库内置API

##### 1、Notify、Wait、NotifyAll

> Notify、Wait、NotifyAll是Java中用于多线程通信的重要方法，它们都是Object类的方法。以下是对这三个方法的详细解释：



**Notify**

1. **功能**：notify()方法用于唤醒一个正在等待该对象的线程。如果有多个线程在等待，那么由线程调度器随机选择一个线程进行唤醒。
2. **使用场景**：在多线程环境中，当某个线程完成了某个任务或达到了某个条件，需要通知其他等待的线程继续执行时，可以使用notify()方法。
3. **注意事项**：
   - notify()方法必须在同步方法或同步块中调用，否则会抛出IllegalMonitorStateException异常。
   - 被唤醒的线程不会立即获得对象的锁，而是需要等待当前持有锁的线程释放锁后才能继续执行。



**Wait**

1. **功能**：wait()方法使当前执行代码的线程进行等待（即将线程放到等待队列中），并释放当前的锁。当其他线程调用该对象的notify()方法或wait()方法等待时间超时，或当前线程被中断时，线程会被唤醒并重新尝试获取锁。
2. **使用场景**：当某个线程需要等待某个条件成立时，可以使用wait()方法。例如，等待某个资源被释放或等待某个任务完成。
3. **注意事项**：
   - wait()方法也必须在同步方法或同步块中调用，否则会抛出IllegalMonitorStateException异常。
   - 在调用wait()方法之前，线程必须已经获得了对象的锁。



**NotifyAll**

1. **功能**：notifyAll()方法用于唤醒所有正在等待该对象的线程。这些线程在被唤醒后会变成等待该对象上的锁的状态，一旦该对象被解锁，它们就会去竞争锁。
2. **使用场景**：当某个条件变化后，需要通知所有等待的线程时，可以使用notifyAll()方法。例如，当某个资源已经被释放或某个任务已经完成，需要让所有等待的线程继续执行时。
3. **注意事项**：
   - 和notify()方法一样，notifyAll()方法也必须在同步方法或同步块中调用。
   - 被唤醒的线程不会立即获得对象的锁，而是需要等待当前持有锁的线程释放锁后才能继续执行。



**总结**

- Notify、Wait、NotifyAll这三个方法是多线程通信的重要工具，它们允许线程在特定条件下进行等待和通知。
- 使用这些方法时需要注意同步和锁的问题，确保线程在正确的状态下进行等待和唤醒。
- 通过合理使用这些方法，可以实现线程之间的有效协作和通信，提高程序的并发性能和可靠性。