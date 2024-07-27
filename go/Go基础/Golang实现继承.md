# `Golang`实现继承

Go 语言不支持传统意义上的类继承。但是，它可以使用匿名字段和接口来实现类似的功能。



### 1、**匿名字段**



匿名字段允许将一个结构体的字段嵌入到另一个结构体中。嵌入的字段就像属于嵌入结构体本身一样。这可以用来模拟单继承。



例如，以下代码演示了如何使用匿名字段从 `Animal` 结构体继承 `Dog` 结构体：



```go
type Animal struct {
  Name string
}

type Dog struct {
  Breed string
  // 匿名嵌入 Animal 结构体
  Animal
}

func main() {
  dog := Dog{
    Name: "Max",
    Breed: "Labrador",
  }

  fmt.Println(dog.Name) // 输出：Max
  fmt.Println(dog.Breed) // 输出：Labrador
}
```



### 2、**接口**



接口是一种定义一组方法的契约。任何实现该接口的类型都可以被视为该接口的类型。这可以用来模拟多继承。



例如，以下代码演示了如何使用接口来实现 `Dog` 和 `Cat` 同时继承 `Pet` 接口：



```go
type Pet interface {
  Name() string
  Eat()
}

type Dog struct {
  Name string
  Breed string
}

func (d Dog) Name() string {
  return d.Name
}

func (d Dog) Eat() {
  fmt.Println("Dog", d.Name, "is eating")
}

type Cat struct {
  Name string
  Color string
}

func (c Cat) Name() string {
  return c.Name
}

func (c Cat) Eat() {
  fmt.Println("Cat", c.Name, "is eating")
}

func main() {
  dog := Dog{
    Name: "Max",
    Breed: "Labrador",
  }

  cat := Cat{
    Name: "Whiskers",
    Color: "black",
  }

  // 作为 Pet 接口的类型
  pets := []Pet{dog, cat}

  for _, pet := range pets {
    fmt.Println(pet.Name())
    pet.Eat()
  }
}
```



这将输出：



```shell
Max
Dog Max is eating
Whiskers
Cat Whiskers is eating
```



请注意，Go 语言中的继承不是强制性的。它是一种可用于组织代码和共享功能的工具，但并非必需。