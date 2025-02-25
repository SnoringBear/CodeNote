## Pre

如题， 一顿操作猛如虎，业务系统总算快发布了，可是JVM的参数还是当初随意设置的那么几个参数， 咋弄？ 系统的流量预估（均值、峰值）导致一系列的评估： 每秒的对象生成大小，新生代 老年代的比例是否合理， 动态年龄判断机制、老年代担保机制会不会被频繁触发，full gc 的频率。。。。

留下一脸懵逼的你在风中瑟瑟发抖~

不要怕 ，今天我们就来看一下如何通过合理的预估来设置系统的JVM参数

[JVM-10虚拟机性能监控与故障处理工具之【JDK的命令行】](https://cloud.tencent.com/developer/article/1862940?from_column=20421&from=20421)



| 名称   | 英文全称                       | 作用                                    |
| :----- | :----------------------------- | :-------------------------------------- |
| jps    | JVM Process Status Tool        | 显示指定系统内所有的HotSPot虚拟机进程   |
| jstat  | JVM Statistics Monitoring Tool | 用于手机HotSpot虚拟机个方面的运行数据   |
| jinfo  | Configuration Info for Java    | 显示虚拟机配置信息                      |
| jmap   | Memory Map for Java            | 生成虚拟机的内存转储快照（headump文件） |
| jhat   | Java Heap Analysis Tool        | 虚拟机堆转储快照分析工具                |
| jstack | Stack Trace For Java           | java堆栈跟踪工具,，显示线程快照         |

------

## jstat



```javascript
[root@artisan ~]# java -version
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)
[root@artisan ~]# jstat -help
Usage: jstat -help|-options
       jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]

Definitions:
  <option>      An option reported by the -options option
  <vmid>        Virtual Machine Identifier. A vmid takes the following form:
                     <lvmid>[@<hostname>[:<port>]]
                Where <lvmid> is the local vm identifier for the target
                Java virtual machine, typically a process id; <hostname> is
                the name of the host running the target Java virtual machine;
                and <port> is the port number for the rmiregistry on the
                target host. See the jvmstat documentation for a more complete
                description of the Virtual Machine Identifier.
  <lines>       Number of samples between header lines.
  <interval>    Sampling interval. The following forms are allowed:
                    <n>["ms"|"s"]
                Where <n> is an integer and the suffix specifies the units as 
                milliseconds("ms") or seconds("s"). The default units are "ms".
  <count>       Number of samples to take before terminating.
  -J<flag>      Pass <flag> directly to the runtime system.
[root@artisan ~]# 
```

官方都说了 jstat -help|-options ，是吧 看看 options的选项吧



```javascript
[root@artisan ~]# jstat -options
-class
-compiler
-gc
-gccapacity
-gccause
-gcmetacapacity
-gcnew
-gcnewcapacity
-gcold
-gcoldcapacity
-gcutil
-printcompilation
[root@artisan ~]# 
```

从中可以看出 , jstat (JVM Statistics Monitoring Tool)用于监视虚拟机各种运行状态信息的[命令行工具](https://cloud.tencent.com/product/cli?from_column=20065&from=20065)。 它可以显示本地或者远程虚拟机进程中的类装载、内存、垃圾回收、JIT编译等运行数据 。

划重点， **垃圾回收** ，这里我们重点看GC垃圾回收 ，因为JVM调优的本质就是为了减少full GC 的发生。

通过不同方式的测试，观察GC的情况，可以很好的预测和分析 每秒的对象生成大小，新生代 老年代的比例是否合理， 动态年龄判断机制、老年代担保机制会不会被频繁触发，full gc 的频率。。。。

选项option代表用户希望查询的虚拟机信息，主要分为3类：类装载、垃圾收集、运行期编译状况。

jstat的主要选项

| 选项              | 作用                                                         |
| :---------------- | :----------------------------------------------------------- |
| -class            | 监视类装载、卸载数量，总空间以及类装载锁耗费的时间           |
| -gc               | 监视Java堆状况，包括eden区、两个Survivor区、老年代、永久代等的容量、已使用空间、GC时间合计等信息 |
| -gccapacity       | 监视内容与-gc基本相同，但输出主要关注Java堆各个区域使用的最大、最小空间 |
| -gcutil           | 监视内容与-gc基本相同，但输出主要关注已使用空间占总空间的百分比 |
| -gccause          | 与-gcutil功能一样，但是会额外输出导致上一次GC产生的原因      |
| -gcnew            | 监视新生代GC状况                                             |
| -gcnewcapacity    | 监视内容与-gcnew 基本相同，但输出主要关注Java堆各个区域使用的最大、最小空间 |
| -gcold            | 监视老年代代GC状况                                           |
| -gcoldcapacity    | 监视内容与-gcold 基本相同，但输出主要关注Java堆各个区域使用的最大、最小空间 |
| -gcpermcapacity   | 输出永久代使用的最大、最小空间                               |
| -compiler         | 输出JIT编译器编译过的方法、耗时等信息                        |
| -printcompilation | 输出已经被JIT编译的方法                                      |

------

### 评估内存使用及GC压力的整体情况 jstat -gc PID



```javascript
[root@artisan ~]# jps
5811 kooteam.jar
10478 Jps
[root@artisan ~]# jstat -gc 5811 
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT   
34944.0 34944.0  0.0    0.0   279616.0 44044.5   699072.0   13979.2   21248.0 20630.5 2560.0 2386.8      0    0.000   1      0.338    0.338
[root@artisan ~]# 
```

参数解读

- S0C：第一个幸存区的大小，单位KB
- S1C：第二个幸存区的大小
- S0U：第一个幸存区的使用大小
- S1U：第二个幸存区的使用大小
- EC：伊甸园区的大小
- EU：伊甸园区的使用大小
- OC：老年代大小
- OU：老年代使用大小
- MC：方法区大小(元空间)
- MU：方法区使用大小
- CCSC:压缩类空间大小
- CCSU:压缩类空间使用大小
- YGC：年轻代垃圾回收次数
- YGCT：年轻代垃圾回收消耗时间，单位s
- FGC：老年代垃圾回收次数
- FGCT：老年代垃圾回收消耗时间，单位s
- GCT：垃圾回收消耗总时间，单位s

当然了，看一次肯定不行啊，一般都是间隔多长时间输出一次，持续观察。

比如 间隔2秒，打印1万次



```javascript
[root@artisan ~]# jstat -gc 5811  2000 10000
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT   
34944.0 34944.0  0.0    0.0   279616.0 56627.5   699072.0   13979.2   21248.0 20630.5 2560.0 2386.8      0    0.000   1      0.338    0.338
34944.0 34944.0  0.0    0.0   279616.0 56627.5   699072.0   13979.2   21248.0 20630.5 2560.0 2386.8      0    0.000   1      0.338    0.338
```

 根据压测，结合这个GC各个分代的情况，加之对业务的理解，推断是否存在不合理的地方。

------

### -gcutil 输出主要关注已使用空间占总空间的百分比

监视内容与-gc基本相同，但输出主要关注已使用空间占总空间的百分比



```javascript
[root@artisan ~]# jstat -gcutil 5811
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT   
  0.00   0.00  24.75   2.00  97.09  93.23      0    0.000     1    0.338    0.338
[root@artisan ~]# jstat -gcutil 5811 2000 10000
```

参数解读

- S0：幸存1区当前使用比例
- S1：幸存2区当前使用比例
- E：伊甸园区使用比例
- O：老年代使用比例
- M：元数据区使用比例
- CCS：压缩使用比例
- YGC：年轻代垃圾回收次数
- FGC：老年代垃圾回收次数
- FGCT：老年代垃圾回收消耗时间
- GCT：垃圾回收消耗总时间

------

### -gccause 额外输出导致上一次GC产生的原因

与-gcutil功能一样，但是会额外输出导致上一次GC产生的原因



```javascript
[root@artisan ~]# jstat -gccause 5811
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT    LGCC                 GCC                 
  0.00   0.00  25.75   2.00  97.09  93.23      0    0.000     1    0.338    0.338 Metadata GC Threshold No GC               
[root@artisan ~]# jstat -gccause 5811 2000 10000
```

- LGCC : 上一次GC的原因

------

### 堆内存统计



```javascript
[root@artisan ~]# jstat -gccapacity 5811
 NGCMN    NGCMX     NGC     S0C   S1C       EC      OGCMN      OGCMX       OGC         OC       MCMN     MCMX      MC     CCSMN    CCSMX     CCSC    YGC    FGC 
349504.0 349504.0 349504.0 34944.0 34944.0 279616.0   699072.0   699072.0   699072.0   699072.0      0.0 1069056.0  21248.0      0.0 1048576.0   2560.0      0     1
[root@artisan ~]# jstat -gccapacity 5811 2000 10000  --> 2秒输出一次 输出1万次
```

参数解读

- NGCMN：新生代最小容量
- NGCMX：新生代最大容量
- NGC：当前新生代容量
- S0C：第一个幸存区大小
- S1C：第二个幸存区的大小
- EC：伊甸园区的大小
- OGCMN：老年代最小容量
- OGCMX：老年代最大容量
- OGC：当前老年代大小
- OC:当前老年代大小
- MCMN:最小元数据容量
- MCMX：最大元数据容量
- MC：当前元数据空间大小
- CCSMN：最小压缩类空间大小
- CCSMX：最大压缩类空间大小
- CCSC：当前压缩类空间大小
- YGC：年轻代gc次数
- FGC：老年代GC次数

------

### 新生代垃圾回收统计



```javascript
[root@artisan ~]# jstat -gcnew 5811
 S0C    S1C    S0U    S1U   TT MTT  DSS      EC       EU     YGC     YGCT  
34944.0 34944.0    0.0    0.0 15  15    0.0 279616.0  56627.5      0    0.000
[root@artisan ~]# jstat -gcnew 5811  2000 10000 --> 2秒输出一次 输出1万次
```

参数解读

- S0C：第一个幸存区的大小
- S1C：第二个幸存区的大小
- S0U：第一个幸存区的使用大小
- S1U：第二个幸存区的使用大小
- TT:对象在新生代存活的次数
- MTT:对象在新生代存活的最大次数
- DSS:期望的幸存区大小
- EC：伊甸园区的大小
- EU：伊甸园区的使用大小
- YGC：年轻代垃圾回收次数
- YGCT：年轻代垃圾回收消耗时间

------

### 新生代内存统计



```javascript
[root@artisan ~]# jstat -gcnewcapacity 5811 
  NGCMN      NGCMX       NGC      S0CMX     S0C     S1CMX     S1C       ECMX        EC      YGC   FGC 
  349504.0   349504.0   349504.0  34944.0  34944.0  34944.0  34944.0   279616.0   279616.0     0     1
[root@artisan ~]# jstat -gcnewcapacity 5811 2000 10000 --> 2秒输出一次 输出1万次
```

参数解读

- NGCMN：新生代最小容量
- NGCMX：新生代最大容量
- NGC：当前新生代容量
- S0CMX：最大幸存1区大小
- S0C：当前幸存1区大小
- S1CMX：最大幸存2区大小
- S1C：当前幸存2区大小
- ECMX：最大伊甸园区大小
- EC：当前伊甸园区大小
- YGC：年轻代垃圾回收次数
- FGC：老年代回收次数

------

### 老年代垃圾回收统计



```javascript
[root@artisan ~]# jstat -gcold 5811 
   MC       MU      CCSC     CCSU       OC          OU       YGC    FGC    FGCT     GCT   
 21248.0  20630.5   2560.0   2386.8    699072.0     13979.2      0     1    0.338    0.338
[root@artisan ~]# jstat -gcold 5811  2000 10000  --> 2秒输出一次 输出1万次
```

参数解读

- MC：方法区大小
- MU：方法区使用大小
- CCSC:压缩类空间大小
- CCSU:压缩类空间使用大小
- OC：老年代大小
- OU：老年代使用大小
- YGC：年轻代垃圾回收次数
- FGC：老年代垃圾回收次数
- FGCT：老年代垃圾回收消耗时间
- GCT：垃圾回收消耗总时间

------

### 老年代内存统计



```javascript
[root@artisan ~]# jstat -gcoldcapacity 5811 
   OGCMN       OGCMX        OGC         OC       YGC   FGC    FGCT     GCT   
   699072.0    699072.0    699072.0    699072.0     0     1    0.338    0.338
[root@artisan ~]# jstat -gcoldcapacity 5811 2000 10000
```

参数解读

- OGCMN：老年代最小容量
- OGCMX：老年代最大容量
- OGC：当前老年代大小
- OC：老年代大小
- YGC：年轻代垃圾回收次数
- FGC：老年代垃圾回收次数
- FGCT：老年代垃圾回收消耗时间
- GCT：垃圾回收消耗总时间

------

### 元数据空间统计



```javascript
[root@artisan ~]# jstat -gcmetacapacity 5811 
   MCMN       MCMX        MC       CCSMN      CCSMX       CCSC     YGC   FGC    FGCT     GCT   
       0.0  1069056.0    21248.0        0.0  1048576.0     2560.0     0     1    0.338    0.338
[root@artisan ~]# jstat -gcmetacapacity 5811  2000 10000
```

参数解读

- MCMN:最小元数据容量
- MCMX：最大元数据容量
- MC：当前元数据空间大小
- CCSMN：最小压缩类空间大小
- CCSMX：最大压缩类空间大小
- CCSC：当前压缩类空间大小
- YGC：年轻代垃圾回收次数
- FGC：老年代垃圾回收次数
- FGCT：老年代垃圾回收消耗时间
- GCT：垃圾回收消耗总时间

------

## 运行情况预估

jstat 会用了哈

主要是利用jstat -gc pid 算出系统的关键参数 ，结合我们之前梳理的理论知识（这个没有，那基本上是不太可行了） ，先设置一些初始值，然后根据压测情况合理调整。

主要观察一下几点

【年轻代对象增长的速率】

可以执行命令 jstat -gc pid 2000 100(每隔2秒执行1次命令，共执行100次)，通过观察EU(eden区的使用)来估算每秒eden大概新增多少对象.

如果系统负载不高，可以把频率2秒调大一些观察整体情况。

要考虑： 系统高峰期和低谷期间的压力，肯定是不一样的，总和考虑。

------

【Young GC的触发频率和每次耗时】

**知道年轻代对象增长速率我们就能推根据eden区的大小推算出Young GC大概多久触发一次**，Young GC的平均耗时可以通过 YGCT/YGC 公式算出，根据结果我们大概就能知道系统大概多久会因为Young GC的执行而卡顿多久。

------

【每次Young GC后有多少对象存活和进入老年代】

这个因为之前已经**大概知道Young GC的频率**，假设是每5分钟一次，那么可以执行命令 jstat -gc pid 300000 10 ，观察每次结果eden，survivor和老年代使用的变化情况，在每次gc后eden区使用一般会大幅减少，survivor和老年代都有可能增长，这些增长的对象就是**每次Young GC后存活的对象，同时还可以看出每次Young GC后进去老年代大概多少对象，从而可以推算出老年代对象增长速率。**

------

【Full GC的触发频率和每次耗时】

知道了**老年代对象的增长速率就可以推算出Full GC的触发频率了**，Full GC的每次耗时可以用公式 FGCT/FGC 计算得出。

优化思路其实简单来说就是**尽量让每次Young GC后的存活对象小于Survivor区域的50%，都留存在年轻代里。尽量别让对象进入老年代。尽量减少Full GC的频率**，避免频繁Full GC对JVM性能的影响。