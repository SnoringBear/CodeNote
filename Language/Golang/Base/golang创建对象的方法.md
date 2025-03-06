# golang创建对象的方法

在 Go 语言中，创建对象通常使用以下几种方法：

### 1. 使用 `new` 关键字

`new` 关键字会分配一个对象的内存，并返回指向该对象的指针，初始化为零值。

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

func main() {
    // 使用 new 创建对象
    // The new built-in function allocates memory. The first argument is a type,
	// not a value, and the value returned is a pointer to a newly
	// allocated zero value of that type.
    p := new(Person) // p 是 *Person 类型
    p.Name = "Alice"
    p.Age = 30
    
    fmt.Println(*p) // 输出: {Alice 30}
}
```

### 2. 使用结构体字面量

通过直接指定结构体的值，可以创建对象，通常用于创建结构体的实例。

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

func main() {
    // 使用结构体字面量创建对象
    p := Person{Name: "Bob", Age: 25}
    
    fmt.Println(p) // 输出: {Bob 25}
}
```

### 3. 使用构造函数

你可以自定义一个构造函数来创建并初始化对象。例如：

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

// 构造函数
func NewPerson(name string, age int) *Person {
    return &Person{Name: name, Age: age}
}

func main() {
    // 使用构造函数创建对象
    p := NewPerson("Charlie", 28)
    
    fmt.Println(*p) // 输出: {Charlie 28}
}
```

### 4. 使用 `&` 运算符

通过结构体字面量创建对象后，也可以通过 `&` 运算符获取其指针。

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

func main() {
    // 使用字面量创建对象，并获取其指针
    p := &Person{Name: "David", Age: 22}
    
    fmt.Println(*p) // 输出: {David 22}
}
```

总结：

- `new` 返回的是指针。
- 结构体字面量直接创建对象。
- 自定义构造函数适用于更复杂的初始化过程。