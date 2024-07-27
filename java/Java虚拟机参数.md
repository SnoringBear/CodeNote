JVM参数有很多，其实我们直接使用默认的JVM参数，不去修改都可以满足大多数情况。但是如果你想在有限的硬件资源下，部署的系统达到最大的运行效率，那么进行相关的JVM参数设置是必不可少的。下面我们就来对这些JVM参数进行详细的介绍。

JVM参数主要分为以下三种（可以根据书写形式来区分）：

## 一**、标准参数**

标准参数，顾名思义，标准参数中包括功能以及输出的结果都是很稳定的，基本上**不会随着JVM版本的变化而变化**。

我们可以通过 -help 命令来检索出所有标准参数。

```java
C:\Users\XG>java --help
用法：java [options] <主类> [args...]
           （执行类）
   或  java [options] -jar <jar 文件> [args...]
           （执行 jar 文件）
   或  java [options] -m <模块>[/<主类>] [args...]
       java [options] --module <模块>[/<主类>] [args...]
           （执行模块中的主类）
   或  java [options] <源文件> [args]
           （执行单个源文件程序）

 将主类、源文件、-jar <jar 文件>、-m 或
 --module <模块>/<主类> 后的参数作为参数
 传递到主类。

 其中，选项包括：

    -cp <目录和 zip/jar 文件的类搜索路径>
    -classpath <目录和 zip/jar 文件的类搜索路径>
    --class-path <目录和 zip/jar 文件的类搜索路径>
                  使用 ; 分隔的, 用于搜索类文件的目录, JAR 档案
                  和 ZIP 档案列表。
    -p <模块路径>
    --module-path <模块路径>...
                  用 ; 分隔的目录列表, 每个目录
                  都是一个包含模块的目录。
    --upgrade-module-path <模块路径>...
                  用 ; 分隔的目录列表, 每个目录
                  都是一个包含模块的目录, 这些模块
                  用于替换运行时映像中的可升级模块
    --add-modules <模块名称>[,<模块名称>...]
                  除了初始模块之外要解析的根模块。
                  <模块名称> 还可以为 ALL-DEFAULT, ALL-SYSTEM,
                  ALL-MODULE-PATH.
    --list-modules
                  列出可观察模块并退出
    -d <module name>
    --describe-module <模块名称>
                  描述模块并退出
    --dry-run     创建 VM 并加载主类, 但不执行 main 方法。
                  此 --dry-run 选项对于验证诸如
                  模块系统配置这样的命令行选项可能非常有用。
    --validate-modules
                  验证所有模块并退出
                  --validate-modules 选项对于查找
                  模块路径中模块的冲突及其他错误可能非常有用。
    -D<名称>=<值>
                  设置系统属性
    -verbose:[class|module|gc|jni]
                  为给定子系统启用详细输出
    -version      将产品版本输出到错误流并退出
    --version     将产品版本输出到输出流并退出
    -showversion  将产品版本输出到错误流并继续
    --show-version
                  将产品版本输出到输出流并继续
    --show-module-resolution
                  在启动过程中显示模块解析输出
    -? -h -help
                  将此帮助消息输出到错误流
    --help        将此帮助消息输出到输出流
    -X            将额外选项的帮助输出到错误流
    --help-extra  将额外选项的帮助输出到输出流
    -ea[:<程序包名称>...|:<类名>]
    -enableassertions[:<程序包名称>...|:<类名>]
                  按指定的粒度启用断言
    -da[:<程序包名称>...|:<类名>]
    -disableassertions[:<程序包名称>...|:<类名>]
                  按指定的粒度禁用断言
    -esa | -enablesystemassertions
                  启用系统断言
    -dsa | -disablesystemassertions
                  禁用系统断言
    -agentlib:<库名>[=<选项>]
                  加载本机代理库 <库名>, 例如 -agentlib:jdwp
                  另请参阅 -agentlib:jdwp=help
    -agentpath:<路径名>[=<选项>]
                  按完整路径名加载本机代理库
    -javaagent:<jar 路径>[=<选项>]
                  加载 Java 编程语言代理, 请参阅 java.lang.instrument
    -splash:<图像路径>
                  使用指定的图像显示启动屏幕
                  自动支持和使用 HiDPI 缩放图像
                  (如果可用)。应始终将未缩放的图像文件名 (例如, image.ext)
                  作为参数传递给 -splash 选项。
                  将自动选取提供的最合适的缩放
                  图像。
                  有关详细信息, 请参阅 SplashScreen API 文档
    @argument 文件
                  一个或多个包含选项的参数文件
    -disable-@files
                  阻止进一步扩展参数文件
    --enable-preview
                  允许类依赖于此发行版的预览功能
要为长选项指定参数, 可以使用 --<名称>=<值> 或
--<名称> <值>。
```

关于这些命令的详细解释，可以参考官网：[https://docs.oracle.com/javase/7/docs/technotes/tools/solaris/java.html](https://docs.oracle.com/javase/7/docs/technotes/tools/solaris/java.html)

-help 也是一个标准参数，再比如使用比较多的 -version也是。

①**、-version**

显示Java的版本信息。

```java
C:\Users\XG>java --version
java 17.0.4 2022-07-19 LTS
Java(TM) SE Runtime Environment (build 17.0.4+11-LTS-179)
Java HotSpot(TM) 64-Bit Server VM (build 17.0.4+11-LTS-179, mixed mode, sharing)
```

## 二、X 参数

对应前面讲的标准化参数，这是非标准化参数。表示在将来的JVM版本中可能会发生改变，但是这类以 -X开始的参数变化的比较小。

我们可以通过 Java -X 命令来检索所有-X 参数。

```java
C:\Users\XG>java -X

    -Xbatch           禁用后台编译
    -Xbootclasspath/a:<以 ; 分隔的目录和 zip/jar 文件>
                      附加在引导类路径末尾
    -Xcheck:jni       对 JNI 函数执行其他检查
    -Xcomp            强制在首次调用时编译方法
    -Xdebug           不执行任何操作。为实现向后兼容而提供。
    -Xdiag            显示附加诊断消息
    -Xfuture          启用最严格的检查，预期将来的默认值。
                      此选项已过时，可能会在
                      未来发行版中删除。
    -Xint             仅解释模式执行
    -Xinternalversion
                      显示比 -version 选项更详细的
                      JVM 版本信息
    -Xlog:<opts>      配置或启用采用 Java 虚拟
                      机 (Java Virtual Machine, JVM) 统一记录框架进行事件记录。使用 -Xlog:help
                      可了解详细信息。
    -Xloggc:<file>    将 GC 状态记录在文件中（带时间戳）。
                      此选项已过时，可能会在
                      将来的发行版中删除。它将替换为 -Xlog:gc:<file>。
    -Xmixed           混合模式执行（默认值）
    -Xmn<size>        为年轻代（新生代）设置初始和最大堆大小
                      （以字节为单位）
    -Xms<size>        设置初始 Java 堆大小
    -Xmx<size>        设置最大 Java 堆大小
    -Xnoclassgc       禁用类垃圾收集
    -Xrs              减少 Java/VM 对操作系统信号的使用（请参见文档）
    -Xshare:auto      在可能的情况下使用共享类数据（默认值）
    -Xshare:off       不尝试使用共享类数据
    -Xshare:on        要求使用共享类数据，否则将失败。
                      这是一个测试选项，可能导致间歇性
                      故障。不应在生产环境中使用它。
    -XshowSettings    显示所有设置并继续
    -XshowSettings:all
                      显示所有设置并继续
    -XshowSettings:locale
                      显示所有与区域设置相关的设置并继续
    -XshowSettings:properties
                      显示所有属性设置并继续
    -XshowSettings:vm
                      显示所有与 vm 相关的设置并继续
    -XshowSettings:system
                      （仅 Linux）显示主机系统或容器
                      配置并继续
    -Xss<size>        设置 Java 线程堆栈大小
    -Xverify          设置字节码验证器的模式
                      请注意，选项 -Xverify:none 已过时，
                      可能会在未来发行版中删除。
    --add-reads <module>=<target-module>(,<target-module>)*
                      更新 <module> 以读取 <target-module>，而无论
                      模块如何声明。
                      <target-module> 可以是 ALL-UNNAMED，将读取所有未命名
                      模块。
    --add-exports <module>/<package>=<target-module>(,<target-module>)*
                      更新 <module> 以将 <package> 导出到 <target-module>，
                      而无论模块如何声明。
                      <target-module> 可以是 ALL-UNNAMED，将导出到所有
                      未命名模块。
    --add-opens <module>/<package>=<target-module>(,<target-module>)*
                      更新 <module> 以在 <target-module> 中打开
                      <package>，而无论模块如何声明。
    --illegal-access=<value>
                      允许或拒绝通过未命名模块中的代码对命名模块中的
                      类型成员进行访问。
                      <value> 为 "deny"、"permit"、"warn" 或 "debug" 之一
                      此选项将在未来发行版中删除。
    --limit-modules <module name>[,<module name>...]
                      限制可观察模块的领域
    --patch-module <module>=<file>(;<file>)*
                      使用 JAR 文件或目录中的类和资源
                      覆盖或增强模块。
    --source <version>
                      设置源文件模式中源的版本。

这些额外选项如有更改, 恕不另行通知。
```

## 三、XX参数

这是我们日常开发中接触到最多的参数类型。这也是非标准化参数，相对来说不稳定，随着JVM版本的变化可能会发生变化，主要用于**JVM调优**和debug。

**注意**：这种参数是我们后续介绍JVM调优讲解最多的参数。

该参数的书写形式又分为两大类：

### **①、Boolean类型**

格式：-XX:[+-] 表示启用或者禁用name属性。

例子：-XX:+UseG1GC（表示启用G1垃圾收集器）

### **②、Key-Value类型**

格式：-XX:= 表示name的属性值为value。

例子：-XX:MaxGCPauseMillis=500（表示设置GC的最大停顿时间是500ms）

## 四、参数详解（持续更新）

本节我们会持续更新罗列一些JVM参数。

**1、打印已经被用户或者当前虚拟机设置过的参数**

```java
-XX:+PrintCommandLineFlags
```

比如：

```java
C:\Users\XG>java -XX:+PrintCommandLineFlags
-XX:ConcGCThreads=3 -XX:G1ConcRefinementThreads=10 -XX:GCDrainStackTargetSize=64 
-XX:InitialHeapSize=265818944 -XX:MarkStackSize=4194304 -XX:MaxHeapSize=4253103104 
-XX:MinHeapSize=6815736 -XX:+PrintCommandLineFlags -XX:ReservedCodeCacheSize=251658240
-XX:+SegmentedCodeCache -XX:+UseCompressedClassPointers -XX:+UseCompressedOops 
-XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation

用法：java [options] <主类> [args...]
           （执行类）
   或  java [options] -jar <jar 文件> [args...]
           （执行 jar 文件）
   或  java [options] -m <模块>[/<主类>] [args...]
       java [options] --module <模块>[/<主类>] [args...]
           （执行模块中的主类）
   或  java [options] <源文件> [args]
           （执行单个源文件程序）

 将主类、源文件、-jar <jar 文件>、-m 或
 --module <模块>/<主类> 后的参数作为参数
 传递到主类。

 其中，选项包括：

    -cp <目录和 zip/jar 文件的类搜索路径>
    -classpath <目录和 zip/jar 文件的类搜索路径>
    --class-path <目录和 zip/jar 文件的类搜索路径>
                  使用 ; 分隔的, 用于搜索类文件的目录, JAR 档案
                  和 ZIP 档案列表。
    -p <模块路径>
    --module-path <模块路径>...
                  用 ; 分隔的目录列表, 每个目录
                  都是一个包含模块的目录。
    --upgrade-module-path <模块路径>...
                  用 ; 分隔的目录列表, 每个目录
                  都是一个包含模块的目录, 这些模块
                  用于替换运行时映像中的可升级模块
    --add-modules <模块名称>[,<模块名称>...]
                  除了初始模块之外要解析的根模块。
                  <模块名称> 还可以为 ALL-DEFAULT, ALL-SYSTEM,
                  ALL-MODULE-PATH.
    --list-modules
                  列出可观察模块并退出
    -d <module name>
    --describe-module <模块名称>
                  描述模块并退出
    --dry-run     创建 VM 并加载主类, 但不执行 main 方法。
                  此 --dry-run 选项对于验证诸如
                  模块系统配置这样的命令行选项可能非常有用。
    --validate-modules
                  验证所有模块并退出
                  --validate-modules 选项对于查找
                  模块路径中模块的冲突及其他错误可能非常有用。
    -D<名称>=<值>
                  设置系统属性
    -verbose:[class|module|gc|jni]
                  为给定子系统启用详细输出
    -version      将产品版本输出到错误流并退出
    --version     将产品版本输出到输出流并退出
    -showversion  将产品版本输出到错误流并继续
    --show-version
                  将产品版本输出到输出流并继续
    --show-module-resolution
                  在启动过程中显示模块解析输出
    -? -h -help
                  将此帮助消息输出到错误流
    --help        将此帮助消息输出到输出流
    -X            将额外选项的帮助输出到错误流
    --help-extra  将额外选项的帮助输出到输出流
    -ea[:<程序包名称>...|:<类名>]
    -enableassertions[:<程序包名称>...|:<类名>]
                  按指定的粒度启用断言
    -da[:<程序包名称>...|:<类名>]
    -disableassertions[:<程序包名称>...|:<类名>]
                  按指定的粒度禁用断言
    -esa | -enablesystemassertions
                  启用系统断言
    -dsa | -disablesystemassertions
                  禁用系统断言
    -agentlib:<库名>[=<选项>]
                  加载本机代理库 <库名>, 例如 -agentlib:jdwp
                  另请参阅 -agentlib:jdwp=help
    -agentpath:<路径名>[=<选项>]
                  按完整路径名加载本机代理库
    -javaagent:<jar 路径>[=<选项>]
                  加载 Java 编程语言代理, 请参阅 java.lang.instrument
    -splash:<图像路径>
                  使用指定的图像显示启动屏幕
                  自动支持和使用 HiDPI 缩放图像
                  (如果可用)。应始终将未缩放的图像文件名 (例如, image.ext)
                  作为参数传递给 -splash 选项。
                  将自动选取提供的最合适的缩放
                  图像。
                  有关详细信息, 请参阅 SplashScreen API 文档
    @argument 文件
                  一个或多个包含选项的参数文件
    -disable-@files
                  阻止进一步扩展参数文件
    --enable-preview
                  允许类依赖于此发行版的预览功能
要为长选项指定参数, 可以使用 --<名称>=<值> 或
--<名称> <值>。
```

**2、最大堆和最小堆内存设置**

**-Xms512M：设置堆内存初始值为512M**

**-Xmx1024M：设置堆内存最大值为1024M**

这里的ms是memory start的简称，mx是memory max的简称，分别代表最小堆容量和最大堆容量。但是别看这里是-X参数，其实这是-XX参数，等价于：

**-XX:InitialHeapSize**

**-XX:MaxHeapSize**

在通常情况下，服务器项目在运行过程中，堆空间会不断的收缩与扩张，势必会造成不必要的系统压力。所以在生产环境中，**JVM的Xms和Xmx要设置成一样的**，能够避免GC在调整堆大小带来的不必要的压力。

**3、Dump异常快照以及以文件形式导出**

**-XX:+HeapDumpOnOutOfMemoryError**

**-XX:HeapDumpPath**

堆内存出现OOM的概率是所有内存耗尽异常中最高的，出错时的堆内信息对解决问题非常有帮助，所以给JVM设置这个参数(**-XX:+HeapDumpOnOutOfMemoryError**)，让JVM遇到OOM异常时能输出堆内信息，并通过（**-XX:+HeapDumpPath**）参数设置堆内存溢出快照输出的文件地址，这对于特别是对相隔数月才出现的OOM异常尤为重要。

这两个参数通常配套使用：

```java
XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=./
```

**4、发送OOM后，执行一个脚本**

**-XX:OnOutOfMemoryError**

**比如这样设置：**

```java
XX:OnOutOfMemoryError="C:\Program Files\Java\jdk1.8.0_152\bin\jconsole.exe"
```

表示发生OOM后，运行jconsole.exe程序。这里可以不用加“”，因为jconsole.exe路径Program Files含有空格。

利用这个参数，我们可以在系统OOM后，自定义一个脚本，可以用来发送邮件告警信息，可以用来重启系统等等。

**5、打印gc信息**

**①、打印GC简单信息**

**-verbose:gc**

**-XX:+PrintGC**

一个是标准参数，一个是-XX参数，都是打印详细的gc信息。通常会打印如下信息：

![](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0078d79f-fe65-4368-967e-3a11335a68e5/Untitled.png#id=o9O9g&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**②、打印详细GC信息**

**-XX:+PrintGCDetails**

**-XX:+PrintGCTimeStamps**

![](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7aa9e2b6-8f7a-45d0-b1db-c2b104031791/Untitled.png#id=yUqZt&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**6、指定GC日志以文件输出**

**-Xloggc:./gc.log**

这个在参数用于将gc日志以文件的形式输出，更方便我们去查看日志，定位问题。

**7、设置永久代大小**

**-XX:MaxPermSize=1280m**

在JDK1.7以及以前的版本中，只有Hotspot 才有Perm区，称为永久代，它在启动时固定大小，很难进行调优。

在某些情况下，如果动态加载类过多，容易产生Perm区的 OOM。比如某个实际 Web 工程中，因为功能点较多，在运行过程中，要不断动态加载很多类，就会出现类似错误：

"Exception in thread 'dubbo client x.x.connect' java.lang.OutOfMemoryError:PermGenspace"

为了解决这个问题，就需要在项目启动时，设定运行参数-XX:MaxPermSize。

**注意：在JDK1.8以后面的版本，使用元空间来代替永久代。在 JDK1.8以及后面的版本中，如果设定参数-XX:MaxPermSize，启动JVM不会报错，但是会提示：**

**Java Hotspot 64Bit Server VM warning：ignoring option MaxPermSize=1280m:support was removed in 8.0**
