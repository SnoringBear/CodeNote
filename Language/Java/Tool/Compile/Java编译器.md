# Java编译器

### 一、前端编译器

Java编译器的主要作用是将人类可读的Java源代码转换为Java虚拟机（JVM）可以执行的字节码。这种转换过程使得Java程序具有“编写一次，到处运行”的特性，即同一个字节码文件可以在任何安装了JVM的设备上运行

#### 1、`javac`编译器

这是Oracle提供的官方Java编译器，也是JDK中的核心组件



#### 2、Java IDE中的编译器

IDE中的编译器通常基于`javac`或进行了扩展和优化，以提供更丰富的开发功能和更好的用户体验



| **特性**         | **Javac** (JDK 官方编译器)   | **IDE 编译器** (如 ECJ、IDEA)                |
| :--------------- | :--------------------------- | :------------------------------------------- |
| **编译模式**     | 全量编译                     | 增量编译（仅编译修改部分，极速反馈）         |
| **错误检查策略** | 严格逐层检查，遇到错误即停止 | 弹性错误恢复（继续解析后续代码，多错误同显） |
| **语法兼容性**   | 严格遵循 JLS                 | 可能支持实验性语法或 IDE 扩展功能            |
| **与 IDE 集成**  | 无                           | 深度整合（实时错误提示、代码补全联动）       |

#### **3、构建工具集成**

构建工具（如 Maven/Gradle）默认调用 `javac`，但可配置替代编译器：

```xml
<!-- Maven 配置使用 Eclipse 编译器 -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <compilerId>eclipse</compilerId>
    </configuration>
    <dependencies>
        <dependency>
            <groupId>org.codehaus.plexus</groupId>
            <artifactId>plexus-compiler-eclipse</artifactId>
            <version>2.8.4</version>
        </dependency>
    </dependencies>
</plugin>
```



#### 4、**选择建议**

- **开发阶段**：优先使用 IDE 编译器，享受实时反馈和高效调试。
- **生产构建**：通过构建工具（Maven/Gradle）调用 `javac` 确保一致性。
- **疑难排查**：若 IDE 与命令行结果不一致，使用 `javac -Xlint` 进行严格检查



#### 5、**IDE 编译器切换**

IntelliJ IDEA 允许在 `Settings > Build, Execution, Deployment > Compiler` 中选择编译器（如 ECJ 或 Javac）。





### 二、**JIT编译器（运行时优化）**

- JIT编译器是JVM中的核心组件之一，它能够在程序运行时将字节码即时编译为机器码。
- JIT编译器通过热点探测技术，识别出频繁执行的代码（热点代码），并将其编译为高效的机器码，从而提高程序的执行效率。

- **类型**：
  - **C1编译器（客户端编译器）**：快速编译，轻度优化，适用于客户端应用（快速启动）。
  - **C2编译器（服务端编译器）**：深度优化，适合长时间运行的服务端应用（高吞吐量）。
  - **分层编译**（JDK 7+）：结合C1与C2，先快速编译再深度优化。
- **触发机制**：基于方法调用计数器和回边计数器识别热点代码。





### 三、**AOT编译器（提前编译）**

- 与JIT编译器不同，AOT编译器在程序运行之前就将Java源代码或字节码编译为机器码。
- AOT编译的好处是可以减少程序启动时的编译时间，但缺点是生成的机器码可能无法充分利用程序运行时的动态信息进行优化。

- **代表工具**：GraalVM Native Image。

- **适用场景**：云原生应用、需要极速启动的微服务。



### 四、 **GraalVM编译器**

- **角色**：替代传统JIT（如C2），支持高级优化（如部分逃逸分析）。
- **优势**：用Java实现，易于维护扩展；支持多语言（JS、Python等）。
- **模式**：可作为JIT使用，或通过Native Image实现AOT。