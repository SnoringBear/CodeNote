## 三、Golang语言特性



### 1、Golang的优势



![img](三、Golang语言特性.assets/1650470888012-e20eedfd-9064-4d4e-b040-d6878aaa96ad.png)





------

![img](三、Golang语言特性.assets/1650471318257-8884275c-9fe9-41de-8251-1bb828d50aa6.png)



![img](三、Golang语言特性.assets/1650471330308-12d4e2a3-7355-46f6-9af4-1ed10e39538e.png)

------

![img](三、Golang语言特性.assets/1650471363769-9ded1e7a-acb0-4d6c-b4c1-61f9b77589b0.png)



```go
package main
  
import (
    "fmt"
    "time"
)

func goFunc(i int) {
    fmt.Println("goroutine ", i, " ...")
}

func main() {
    for i := 0; i < 10000; i++ {
        go goFunc(i) //开启一个并发协程
    }

    time.Sleep(time.Second)
}
```



![img](三、Golang语言特性.assets/1650471374246-adf62ac5-7eba-45c9-bcd7-ccc2f8141640.png)

------

![img](三、Golang语言特性.assets/1650471446832-5722e0a9-5522-469b-9ea9-296c373e3d66.png)



![img](三、Golang语言特性.assets/1650471454878-bf9c4abc-62c5-42f8-b595-b16d99d10743.png)

------

![img](三、Golang语言特性.assets/1650471465058-b5db8451-e1d8-4ce4-a572-cc3d8be9bdc1.png)

------

![img](三、Golang语言特性.assets/1650471474504-6de5ec53-2447-4bdd-8a08-36ae40283ece.png)



### 2、Golang适合做什么



**(1)、云计算基础设施领域**



代表项目：docker、kubernetes、etcd、consul、cloudflare CDN、七牛云存储等。



**(2)、基础后端软件**



代表项目：tidb、influxdb、cockroachdb等。



**(3)、微服务**



代表项目：go-kit、micro、monzo bank的typhon、bilibili等。



**(4)、互联网基础设施**



代表项目：以太坊、hyperledger等。

------

### 3、Golang明星作品



![img](三、Golang语言特性.assets/1650471498432-166e36fd-6294-460c-bbcd-96f6e784f8a9.png)



![img](三、Golang语言特性.assets/1650471506905-c3bf704e-d2fc-41e1-8e01-0a4907ae28fc.png)



![img](三、Golang语言特性.assets/1650471515654-27569b7e-d67a-45f6-8d8f-54bd616da975.png)



### 4、Golang的不足



1、包管理，大部分包都在**github**上



2、无泛化类型

(Golang 1.18+已经支持泛型)



3、所有**Excepiton**都用**Error**来处理(比较有争议)。



4、对**C**的降级处理，并非无缝，没有**C**降级到**asm**那么完美(序列化问题)