# 启动jar包底层细节

`java -jar`命令启动JAR包的过程涉及多个底层细节，这些细节对于理解Java程序的执行流程至关重要。以下是该过程的详细解析：



### 一、JAR包的结构

JAR包是一种包含Java类文件、资源文件和元数据的归档文件格式。它本质上是一个压缩文件，通常以“.jar”为扩展名。一个典型的JAR包可能包含以下内容：

- 编译后的`.class`文件

- 资源文件（如配置文件、图片等）

- `META-INF`目录

  - maven目录【使用maven构建项目才有】

  - `MANIFEST.MF`文件（可选）,`MANIFEST.MF`文件是一个清单文件

    ```bash
    Manifest-Version: 1.0
    
    Class-Path: ./ config/ lib/CommonModule-0.0.1.jar lib/lombok-1.18.22.j
     ar lib/log4j-core-2.17.1.jar lib/log4j-api-2.17.1.jar lib/log4j-slf4j
     -impl-2.17.1.jar lib/slf4j-api-1.7.25.jar lib/netty-all-4.1.68.Final.
     jar lib/protostuff-core-1.7.4.jar lib/protostuff-api-1.7.4.jar lib/pr
     otostuff-runtime-1.7.4.jar lib/protostuff-collectionschema-1.7.4.jar 
     lib/jedis-4.2.3.jar lib/commons-pool2-2.11.1.jar lib/json-20211205.ja
     r lib/gson-2.8.9.jar lib/mysql-connector-java-5.1.49.jar lib/mybatis-
     3.5.9.jar lib/p6spy-3.9.1.jar lib/mybatis-plus-3.5.1.jar lib/mybatis-
     plus-extension-3.5.1.jar lib/mybatis-plus-core-3.5.1.jar lib/mybatis-
     plus-annotation-3.5.1.jar lib/jsqlparser-4.3.jar lib/httpasyncclient-
     4.1.4.jar lib/httpcore-4.4.10.jar lib/httpcore-nio-4.4.10.jar lib/htt
     pclient-4.5.6.jar lib/commons-codec-1.10.jar lib/commons-logging-1.2.
     jar lib/javassist-3.28.0-GA.jar lib/byte-buddy-agent-1.11.22.jar lib/
     snakeyaml-1.30.jar lib/hutool-all-2023_08_04_01.jar lib/fastjson2-2.0
     .50.jar
    
    Build-Jdk-Spec: 1.8
    
    Created-By: Maven JAR Plugin 3.2.2
    
    Main-Class: server.game.GameServer
    ```

    

    - **Main-Class**：指定程序的入口类（包含 `main` 方法的类）
    - **Class-Path**：指定依赖的其他JAR包或目录【可以在maven构建项目的时候设置】



### 二、`java -jar`命令的执行流程

1. **启动Java虚拟机（JVM）**：
   当执行`java -jar`命令时，首先会启动Java虚拟机。JVM是一个虚拟的计算机，负责加载、验证、解释和执行Java字节码。
2. **加载JAR包**：
   JVM会根据命令中的参数找到要执行的JAR文件，并将其加载到内存中。这个过程包括读取JAR文件的元数据，如`MANIFEST.MF`文件等，以确定项目的入口点。
3. **解析`MANIFEST.MF`文件**：
   JVM会解析`MANIFEST.MF`文件，查找`Main-Class`属性。该属性指定了JAR包的主类，即包含`main`方法的类。例如，`Main-Class: com.example.MainApp`指定了主类为`com.example.MainApp`。
4. **加载主类**：
   JVM会根据`Main-Class`属性指定的类名，使用类加载器机制加载主类。类加载器会按照特定的顺序（如引导类加载器、扩展类加载器和应用类加载器等）去查找和加载所需的类。
5. **链接和初始化**：
   在加载主类之后，JVM会进行链接和初始化操作。链接阶段包括符号引用的解析和验证等步骤；初始化阶段则包括执行类的静态初始化器和为类的静态字段分配和初始化内存空间等步骤。
6. **执行`main`方法**：
   一旦主类被加载和初始化完成，JVM就会执行主类中的`main`方法。这是Java程序的入口点，负责启动整个程序的执行流程。



### 三、注意事项

- 如果JAR包没有包含`MANIFEST.MF`文件或该文件没有指定`Main-Class`属性，则无法使用`java -jar`命令启动JAR包。此时，需要手动指定主类和类路径来运行程序。
- 在执行`java -jar`命令时，JVM会使用双亲委派模型来查找和加载类。这种机制保证了Java程序的稳定性和安全性，防止类文件被恶意篡改。
- 除了加载和执行主类之外，JVM还会进行垃圾回收、线程管理等操作，以保证Java程序的正确性和性能。

综上所述，`java -jar`命令启动JAR包的过程涉及JVM的启动、JAR包的加载、`MANIFEST.MF`文件的解析、主类的加载和初始化以及`main`方法的执行等多个底层细节。理解这些细节有助于更好地掌握Java程序的执行流程和调试技巧。