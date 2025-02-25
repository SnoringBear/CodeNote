# Java标准库

####  Class  

isAssignableFrom()方法与instanceof关键字的区别总结为以下两个点：

* isAssignableFrom()方法是从类继承的角度去判断，instanceof关键字是从实例继承的角度去判断。
* isAssignableFrom()方法是判断是否为某个类的父类，instanceof关键字是判断是否某个类的子类。





#### URLDecoder

* decode()
  * url中的特殊字符有这么多.在前后端传输过程中,可能会对这些字符进行特殊处理,导致后台进行使用原字符串时,出现异常





####  Arrays

* fill()
  * 填充数组







#### Cloneable接口

> Cloneable接口是Java中的一个标记接口（也称为标识接口），它没有定义任何方法，只是用来表明一个类的对象能够被合法地克隆。在Java中，克隆是指创建一个对象的副本，即一个新的对象，其内容与原对象相同



**使用方法**

* **实现Cloneable接口**：一个类要实现克隆功能，首先需要实现Cloneable接口。这并不会给类增加任何新的方法，但它会向Java运行时环境表明，这个类的对象允许使用clone()方法进行复制
* **重写clone()方法**：虽然Cloneable接口本身不包含任何方法，但按照惯例，实现Cloneable接口的类应该重写Object类的clone()方法。这是因为clone()方法是在Object类中定义的，而Object类是Java中所有类的基类。重写clone()方法是为了提供具体的克隆逻辑
* **调用clone()方法**：在实现了Cloneable接口并重写了clone()方法的类中，就可以通过调用clone()方法来创建对象的副本了。需要注意的是，clone()方法是一个受保护的方法（protected），因此它只能在同一个包内的类或者子类中被调用



**克隆类型**

* **浅克隆**：浅克隆是指创建一个新对象，这个新对象的所有字段都与原对象的字段值相同。但对于引用类型字段，新对象只是复制了引用，而不是引用指向的实际对象。换句话说，浅克隆后的新对象和原对象共享同一个引用对象。因此，如果原对象中的引用类型字段被修改，那么新对象中的对应字段也会受到影响
* **深克隆**：深克隆会连同对象的引用类型变量一起拷贝，这样原对象和拷贝得到的对象在修改对象的属性值时不会相互影响。实现深克隆需要重写clone()方法，并在方法中递归地克隆所有引用类型的字段



**Java通过序列化来实现深度克隆带来的问题**

* 性能问题
  * 序列化和反序列化开销：
    * 序列化和反序列化过程需要消耗一定的计算资源和时间，特别是对于大型对象或复杂对象结构，这种开销可能更加显著。

    * 在性能敏感的应用中，频繁进行序列化和反序列化操作可能会导致性能瓶颈。

  * 内存占用：
    * 序列化后的对象数据需要存储在内存中，如果对象很大或数量很多，会占用大量的内存空间

    * 此外，序列化和反序列化过程中产生的临时对象（如字节数组）也会增加内存使用

* 安全性问题
  * 远程代码执行（RCE）风险

  * 反序列化注入攻击

* 兼容性问题
  * 版本兼容性：
    * serialVersionUID用于确保序列化的兼容性。如果类的结构发生变化（如添加或删除字段），但serialVersionUID没有相应更新，那么在反序列化时可能会抛出InvalidClassException异常

    * 这要求开发人员在修改类结构时必须谨慎处理serialVersionUID的更新问题

  * 不同JVM间的兼容性
    * 虽然Java序列化机制在大多数情况下是跨平台的，但在某些特定情况下（如使用不同版本的JVM或不同的序列化库），可能会遇到兼容性问题

* 限制和约束
  * 需要实现Serializable接口：
  * transient关键字的使用
  * 循环引用





#### Java排序

> Java中的排序规则可以根据不同的需求和场景来定义，主要包括自然排序、自定义排序以及使用Java内置的排序工具类等方法。以下是对Java排序规则的详细阐述：



**一、自然排序**

自然排序是指根据元素的自然顺序（如数值大小、字母顺序等）进行排序。Java中的许多集合类都支持自然排序，例如`List`接口的实现类`ArrayList`和`LinkedList`，以及`Set`接口的实现类`TreeSet`等。

对于支持自然排序的集合类，当插入元素时，它们会根据元素的自然顺序进行排列。例如，对于`TreeSet`来说，它会根据元素的`compareTo`方法来确定元素的顺序。



**二、自定义排序**

自定义排序是指根据用户定义的规则进行排序。Java提供了两种方式来实现自定义排序：`Comparable`接口和`Comparator`接口。

1. **Comparable接口**：
   - `Comparable`接口位于`java.lang`包中。
   - 需要在要排序的类上实现该接口，并重写`compareTo`方法。
   - 在调用排序方法时，只需指定集合名即可，不需要额外传入比较器。
2. **Comparator接口**：
   - `Comparator`接三、Java内置的排序工具类口位于`java.util`包中。
   - 需要在要比较的类的外部实现该接口，并重写`compare`方法。
   - 在调用排序方法时，需要传入实现了`Comparator`接口的比较器对象。

例如，有一个`Student`类，其中包含姓名和年龄属性，希望按照年龄从大到小的顺序对`Student`对象进行排序。可以实现一个`AgeComparator`类，该类实现了`Comparator<Student>`接口，并重写了`compare`方法。然后，在使用`Collections.sort`方法时，传入`AgeComparator`的实例即可。



**三、Java内置的排序工具类**

Java提供了两个常用的排序工具类：`Arrays.sort()`和`Collections.sort()`。

1. **Arrays.sort()**：
   - 用于对数组进行排序。
   - 支持基本数据类型数组（如`int[]`、`double[]`等）和对象数组（如`String[]`、`Student[]`等）的排序。
   - 对于对象数组，要求数组中的元素必须实现了`Comparable`接口，或者传入了一个实现了`Comparator`接口的比较器。
2. **Collections.sort()**：
   - 用于对`List`集合进行排序。
   - 支持自然排序和自定义排序。
   - 在进行自定义排序时，需要传入一个实现了`Comparator`接口的比较器





#### AutoCloseable接口

> AutoCloseable接口是Java 7中引入的一个重要接口，主要用于定义一种可以被自动关闭的资源。以下是关于AutoCloseable接口的详细介绍：



**一、接口定义**

AutoCloseable接口位于java.lang包中，它只包含一个方法：close()。该方法的定义如下：

```java
public interface AutoCloseable {
    void close() throws Exception;
}
```



**二、主要用途**

AutoCloseable接口的主要用途是简化资源管理，特别是需要显式关闭的资源（如文件、数据库连接、网络连接等）。通过实现AutoCloseable接口，并使用try-with-resources语句，可以确保这些资源在使用完毕后被正确关闭，从而避免内存泄漏和资源耗尽等问题。



**三、try-with-resources语句**

try-with-resources语句是Java 7中引入的一种新的异常处理机制，它能够自动管理实现了AutoCloseable接口的资源。在使用try-with-resources语句时，需要在try关键字后面的圆括号中声明一个或多个资源（这些资源必须是实现了AutoCloseable接口的对象）。当try语句块执行完毕后，无论是正常结束还是因为异常而结束，这些资源都会自动调用它们的close()方法来释放资源。



以下是一个使用try-with-resources语句的示例：

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
 
public class AutoCloseableExample {
    public static void main(String[] args) {
        String filePath = "example.txt";
        try (BufferedReader br = new BufferedReader(new FileReader(filePath))) {
            String line;
            while ((line = br.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        // 注意：这里不需要显式调用 br.close()，因为 try-with-resources 会自动处理
    }
}
```

在上面的例子中，BufferedReader对象br被声明在try语句的圆括号中。当try语句块执行完毕后，无论是否发生异常，br的close()方法都会被自动调用，从而确保文件资源被正确释放。



**四、自定义实现**

除了Java标准库中的类实现AutoCloseable接口外，用户也可以自定义类来实现AutoCloseable接口，并在close()方法中定义资源释放的逻辑。例如：

```java
import lombok.extern.slf4j.Slf4j;
 
@Slf4j
public class PerfTracker {
    private final long startTime;
    private final String methodName;
 
    private PerfTracker(String methodName) {
        this.startTime = System.currentTimeMillis();
        this.methodName = methodName;
    }
 
    public static TimerContext start() {
        StackTraceElement stackTrace = Thread.currentThread().getStackTrace()[2];
        String[] split = stackTrace.getClassName().split("\\.");
        String s = "at " + stackTrace.getClassName() + "." + stackTrace.getMethodName() + "(" + split[split.length - 1] + ".java:" + stackTrace.getLineNumber() + ")";
        return new TimerContext(s);
    }
 
    public static class TimerContext implements AutoCloseable {
        private final PerfTracker tracker;
 
        private TimerContext(String methodName) {
            this.tracker = new PerfTracker(methodName);
        }
 
        @Override
        public void close() {
            long executeTime = System.currentTimeMillis() - tracker.startTime;
            if (executeTime > 500) {
                log.warn("慢sql查询告警:{} 耗时 {}ms", tracker.methodName, executeTime);
            }
        }
    }
}
```

使用：

```java
public static void main(String[] args) {
    try (PerfTracker.TimerContext ignored = PerfTracker.start()) {
        // 执行一些操作，例如数据库查询
    }
}
```

在上面的例子中，PerfTracker类用于性能追踪，而TimerContext类实现了AutoCloseable接口，用于在try-with-resources语句中自动记录操作耗时。

综上所述，AutoCloseable接口为Java提供了一种简洁而有效的方式来管理资源，确保资源在使用完毕后能够被正确释放。





#### String

> String、StringBuffer、StringBuilder在Java中都是用来处理字符串的类，但它们在使用和功能上有一些显著的区别。以下是对这三者的详细对比：



**一、基本特性**

1. **String**
   - **不可变性**：String类的对象是不可变的，即一旦创建了一个String对象，其内容就不能被改变。如果需要修改字符串，实际上是创建了一个新的字符串对象。
   - **线程安全**：由于String对象的不可变性，它在多线程环境中是线程安全的，因为多个线程可以安全地共享同一个String对象而不需要担心数据竞争。
   - **性能**：由于每次修改都会创建新的字符串对象，因此在频繁修改字符串的场景下，String的性能可能会受到影响。
2. **StringBuffer**
   - **可变性**：StringBuffer类的对象是可变的，即可以在不创建新对象的情况下修改字符串的内容。
   - **线程安全**：StringBuffer是线程安全的，它的所有公开方法都是同步的（使用synchronized关键字修饰），因此可以在多线程环境中安全地使用。
   - **性能**：虽然StringBuffer提供了线程安全，但由于同步操作的开销，它在单线程环境中的性能可能不如StringBuilder。
3. **StringBuilder**
   - **可变性**：StringBuilder类的对象也是可变的，与StringBuffer类似，可以在不创建新对象的情况下修改字符串的内容。
   - **线程安全**：StringBuilder不是线程安全的，它的方法没有使用synchronized关键字修饰。因此，在多线程环境中使用StringBuilder可能会导致数据竞争和不一致的情况。但在单线程环境中，StringBuilder的性能通常优于StringBuffer。
   - **性能**：由于没有线程安全的开销，StringBuilder在单线程环境中提供了更高的性能。





**二、使用场景**

1. **String**：适用于需要存储不变字符串的场景，如字符串常量、配置项等。由于String的不可变性和线程安全性，它在多线程环境中非常有用。
2. **StringBuffer**：适用于需要在多线程环境中安全地修改字符串的场景。由于它是线程安全的，因此可以在多个线程之间共享和修改同一个StringBuffer对象。
3. **StringBuilder**：适用于在单线程环境中频繁修改字符串的场景。由于它提供了更高的性能而没有线程安全的开销，因此是单线程环境下修改字符串的首选。





**三、示例代码**

以下是一个简单的示例代码，展示了String、StringBuffer和StringBuilder的基本用法：

```java
public class StringExample {
    public static void main(String[] args) {
        // String示例
        String str = "Hello";
        String str1 = str + " World"; // 创建了一个新的String对象
        System.out.println(str1); // 输出：Hello World
 
        // StringBuffer示例
        StringBuffer sb = new StringBuffer("Hello");
        StringBuffer sb1 = sb.append(" World"); // 在原有对象上修改，没有创建新对象
        System.out.println(sb1.toString()); // 输出：Hello World，注意sb和sb1实际上是同一个对象
 
        // StringBuilder示例
        StringBuilder sbBuilder = new StringBuilder("Hello");
        StringBuilder sbBuilder1 = sbBuilder.append(" World"); // 在原有对象上修改，没有创建新对象
        System.out.println(sbBuilder1.toString()); // 输出：Hello World，注意sbBuilder和sbBuilder1实际上是同一个对象
    }
}
```





**四、总结**

- **String**：不可变，线程安全，适用于存储不变字符串的场景。
- **StringBuffer**：可变，线程安全，适用于多线程环境中安全地修改字符串的场景。
- **StringBuilder**：可变，非线程安全，适用于单线程环境中频繁修改字符串的场景，并提供了更高的性能。

在选择使用哪个类时，应根据具体的使用场景和需求来决定。
