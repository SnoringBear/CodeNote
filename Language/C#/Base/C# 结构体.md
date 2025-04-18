*在 C# 中，结构体（struct）是一种值类型（value type），用于组织和存储相关数据。*

在 C# 中，结构体是值类型数据结构，这样使得一个单一变量可以存储各种数据类型的相关数据。

**struct** 关键字用于创建结构体。



## C# 结构的特点

结构提供了一种轻量级的数据类型，适用于表示简单的数据结构，具有较好的性能特性和值语义：

- 结构可带有方法、字段、索引、属性、运算符方法和事件，适用于表示轻量级数据的情况，如坐标、范围、日期、时间等。
- 结构可定义构造函数，但不能定义析构函数。但是，您不能为结构定义无参构造函数。无参构造函数(默认)是自动定义的，且不能被改变。
- 与类不同，结构不能继承其他的结构或类。
- 结构不能作为其他结构或类的基础结构。
- 结构可实现一个或多个接口。
- 结构成员不能指定为 abstract、virtual 或 protected。
- 当您使用 **New** 操作符创建一个结构对象时，会调用适当的构造函数来创建结构。与类不同，结构可以不使用 New 操作符即可被实例化。
- 如果不使用 New 操作符，只有在所有的字段都被初始化之后，字段才被赋值，对象才被使用。
- 结构变量通常分配在栈上，这使得它们的创建和销毁速度更快。但是，如果将结构用作类的字段，且这个类是引用类型，那么结构将存储在堆上。
- 结构默认情况下是可变的，这意味着你可以修改它们的字段。但是，如果结构定义为只读，那么它的字段将是不可变的。

## 类 vs 结构

类和结构在设计和使用时有不同的考虑因素，类适合表示复杂的对象和行为，支持继承和多态性，而结构则更适合表示轻量级数据和值类型，以提高性能并避免引用的管理开销。

类和结构有以下几个基本的不同点：

**值类型 vs 引用类型：**

- **结构是值类型（Value Type）：** 结构是值类型，它们在栈上分配内存，而不是在堆上。当将结构实例传递给方法或赋值给另一个变量时，将复制整个结构的内容。
- **类是引用类型（Reference Type）：** 类是引用类型，它们在堆上分配内存。当将类实例传递给方法或赋值给另一个变量时，实际上是传递引用（内存地址）而不是整个对象的副本。

**继承和多态性：**

- **结构不能继承：** 结构不能继承其他结构或类，也不能作为其他结构或类的基类。
- **类支持继承：** 类支持继承和多态性，可以通过派生新类来扩展现有类的功能。

**默认构造函数：**

- **结构不能有无参数的构造函数：** 结构不能包含无参数的构造函数。
- **类可以有无参数的构造函数：** 类可以包含无参数的构造函数，如果没有提供构造函数，系统会提供默认的无参数构造函数。

**赋值行为：**

- 类型为类的变量在赋值时存储的是引用，因此两个变量指向同一个对象。
- 结构变量在赋值时会复制整个结构，因此每个变量都有自己的独立副本。

**传递方式：**

- 类型为类的对象在方法调用时通过引用传递，这意味着在方法中对对象所做的更改会影响到原始对象。
- 结构对象通常通过值传递，这意味着传递的是结构的副本，而不是原始结构对象本身。因此，在方法中对结构所做的更改不会影响到原始对象。

**可空性：**

- **结构体是值类型，不能直接设置为 \**null\**：**因为 **null** 是引用类型的默认值，而不是值类型的默认值。如果你需要表示结构体变量的缺失或无效状态，可以使用 **Nullable<T>** 或称为 **T?** 的可空类型。
- **类默认可为null：** 类的实例默认可以为 `null`，因为它们是引用类型。

**性能和内存分配：**

- **结构通常更轻量：** 由于结构是值类型且在栈上分配内存，它们通常比类更轻量，适用于简单的数据表示。
- **类可能有更多开销：** 由于类是引用类型，可能涉及更多的内存开销和管理。