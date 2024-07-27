 一、线程状态  
![Untitled.png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690944603750-e3be6df3-3baf-49b8-8029-ca518a940555.png#averageHue=%23a19590&clientId=u07c3495f-e60b-4&from=paste&height=1091&id=ua4d22b59&originHeight=1091&originWidth=2000&originalType=binary&ratio=1&rotation=0&showTitle=false&size=735003&status=done&style=none&taskId=uddcc1191-c7dc-4d87-a0cf-dc8c6d2890c&title=&width=2000)
```go
// 实现线程的方式 1  继承Thread类
// 实现线程的方式 2  实现Runnable接口
// 通过线程池的方式创建   Executors工具类


// Thread 的三个方法   sleep()  这个时间段CPU不会调用
// yield() 从运行状态切换到就绪状态      有可能CPU下次调度依然是它
// join()   把当前线程加入   只有执行加入线程的内容,才会执行后面的内容

// 锁   锁任何对象，只有持有锁才能访问同步代码


// volatile 内存可见性
// synchronized   volatile  两个的区别  保证了原子性与可见性


// 方法上加synchronized  相当于锁T.class或者T的实例,synchronized锁的是对象而不是代码
// 锁对象不能使用String常量   基础数据类型{例如:Long、Integer等}
```

写入(加锁) 				读取(是否需要加锁)

根据业务来决定，如果只是单纯的读取，业务允许中间过程，可以不用加锁，如果不允许，需要加锁

程序在执行过程中，如果发生异常，锁会被自动释放掉

```java
// synchronized 是可重入锁   ？ 例如一个子类synchronized方法调用父类的相同方法，如果不可重入，父子类调用会产生死锁

//  synchronized底层实现
//  JDK 早期       重量级     ----os申请

//  锁升级
//  sync(Object)
//  markword 记录这个线程的id(偏向锁)

//  如果有线程争用    升级为自旋锁
//  旋转10次以后{自旋会占用CPU}
//  升级为重量级锁

//  执行时间短，线程数量少    使用偏向锁
//  执行时间长，线程数量多    使用系统级锁
```

Volatile测试

```java
public class VolatileDemo {
    volatile boolean running = true; // 内存可见性   其他线程修改的值，内外内存可以看见
    public static void main(String[] args) {
        VolatileDemo demo = new VolatileDemo();
        new Thread(demo::m,"demo").start();
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {

        }
        demo.running = false;
    }

    void m(){
        System.out.println("start");
        while (running){

        }
        System.out.println("end");
    }
}
```

![Untitled (1).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690945010640-bed49271-c378-4407-a023-c60c68783f2d.png#averageHue=%23171615&clientId=u07c3495f-e60b-4&from=paste&height=1125&id=u4e159f73&originHeight=1125&originWidth=2000&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1289026&status=done&style=none&taskId=u01903b98-9920-41ae-aeb1-83b1cdd7ec7&title=&width=2000)

上图使用到的技术：MESI，CPU的一种缓存一致性协议

指令重排序：现代CPU为了高效运行，所采取的一种策略,一行代码可能是由多条指令完成，这些指令的顺序可能会被打乱。(单利模式:双检查锁)

类被加载以后，由JVM对类进行初始化

### 锁(`**synchronized**`)的优化

1、锁的细化【同步代码越少越好,在锁竞争强度不高的情况下】

2、锁的粗化 【有很多细锁出现的情况下，可以使用一个锁大面积锁住】

### CAS（无锁优化 ）

本质:自旋

最后调用Unsafe.`compareAndSet`   这是一个native方法
