## Golang泛型

在 Go 1.18 版本开始，Go 语言引入了泛型（Generics）的支持。泛型允许你编写能够处理任意类型的代码，而无需为每个具体类型编写单独的实现。这是通过引入类型参数的方式实现的，类似于其他静态类型语言中的泛型特性，如 C++、Java 和 Rust。

### 一、泛型的基本语法

##### 1、**类型参数的定义**

类型参数使用方括号 `[]` 来定义。例如，`T` 是一个类型参数

```go
func Print[T any](value T) {
    fmt.Println(value)
}
```

在这个例子中，`Print` 函数接收一个类型参数 `T`，然后这个类型 `T` 可以是任何类型（`any` 关键字用于指定没有具体约束的泛型类型）。

##### 2、**使用泛型的函数**：

你可以使用泛型来定义函数、方法、以及结构体。

- **函数**：

  ```go
  func Add[T int | float64](a, b T) T {
      return a + b
  }
  ```

  在这里，`Add` 函数使用了类型参数 `T`，并且约束 `T` 只能是 `int` 或 `float64` 类型。

- **结构体**：

  ```go
  type Pair[T any] struct {
      first, second T
  }
  ```

  泛型结构体 `Pair` 有两个相同类型的字段 `first` 和 `second`。

##### 3、**类型约束**： 

类型约束定义了泛型参数可以接受的类型。Go 提供了一些预定义的类型约束（如 `any` 代表任意类型），你也可以通过接口自定义约束。

- **预定义类型约束**：

  ```go
  func Min[T constraints.Ordered](a, b T) T {
      if a < b {
          return a
      }
      return b
  }
  ```

  `constraints.Ordered` 是 Go 中一个预定义的约束，表示 `T` 必须是可以比较大小的类型（如 `int`、`float64`、`string` 等）。

- **自定义接口作为约束**： 你也可以定义自己的接口作为泛型的约束：

  ```go
  type Stringer interface {
      String() string
  }
  
  func PrintString[T Stringer](s T) {
      fmt.Println(s.String())
  }
  ```

  在这里，`PrintString` 函数要求类型参数 `T` 必须实现 `Stringer` 接口。

### 二、泛型的优势

##### 1、**代码复用**：

泛型允许你编写更通用的代码，减少重复代码的编写。

##### 2、**类型安全**：

与使用 `interface{}` 不同，泛型保证了在编译时进行类型检查，因此可以避免类型转换错误。

##### 3、**灵活性**：

通过泛型，代码可以更灵活，能够适应更多种类的输入类型。

### 三、示例：泛型栈实现

一个使用泛型的栈（Stack）实现示例：

```go
package main

import "fmt"

// 定义泛型栈
type Stack[T any] struct {
    elements []T
}

// Push 方法
func (s *Stack[T]) Push(element T) {
    s.elements = append(s.elements, element)
}

// Pop 方法
func (s *Stack[T]) Pop() (T, bool) {
    if len(s.elements) == 0 {
        var zeroValue T
        return zeroValue, false
    }
    index := len(s.elements) - 1
    element := s.elements[index]
    s.elements = s.elements[:index]
    return element, true
}

func main() {
    var intStack Stack[int]
    intStack.Push(10)
    intStack.Push(20)
    fmt.Println(intStack.Pop()) // 输出: 20 true

    var stringStack Stack[string]
    stringStack.Push("hello")
    stringStack.Push("world")
    fmt.Println(stringStack.Pop()) // 输出: world true
}
```

### 四、总结

Go 泛型通过类型参数和类型约束提供了更加通用和灵活的编程能力，避免了编写重复代码的繁琐，同时仍然保持了编译时的类型安全。这是 Go 语言发展中的重要特性，为开发者带来了更多选择