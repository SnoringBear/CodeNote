# Golang标准库

## 一、signal.Notify函数

signal.Notify 是Go标准库 os/signal 包中的一个函数，用于注册信号监听器

##### 1、常用信号类型

* syscall.SIGHUP 是挂断信号（Hangup），通常在终端断开连接时发送给进程
* syscall.SIGQUIT 是退出请求信号（Quit），通常由用户按下 Ctrl+\ 发送，生成核心转储后退出进程
* syscall.SIGTERM 是终止信号（Termination），温和地请求进程终止，可以被捕获和处理
* syscall.SIGINT 是中断信号（Interrupt），通常由用户按下 Ctrl+C 发送，也请求进程终止。

##### 2、代码示例

```go
c := make(chan os.Signal, 1)
signal.Notify(c, syscall.SIGHUP, syscall.SIGQUIT, syscall.SIGTERM, syscall.SIGINT)
// 这段代码的作用是设置一个信号处理器，当进程接收到 SIGHUP, SIGQUIT, SIGTERM 或 SIGINT 信号中的任何一个时，它会在 c 通道上发送一个信号值。
// 这样，你的程序就可以通过监听 c 通道来知道何时发生了这些事件，并可以执行相应的清理工作，比如关闭打开的文件、停止服务、保存状态等，实现优雅的退出。

// 当有信号到达时，这个循环会停止
go func() {
    sig := <-c
    fmt.Printf("Received signal: %s\n", sig)
    // 这里执行清理工作
    cleanup()
    os.Exit(0)
}()

// 你的主要业务逻辑在这里
mainLogic()

```



## 二、slice

slice并不是真正意义上的动态数组，而是一个引用类型。slice总是指向一个底层array

当引用类型作为函数参数时，可能会修改原内容数据



#### 1、主要特性

* 引用类型

* 切片状态

  * 空切片
  * 零切片
  * nil 切片

* 非线程安全

  slice不支持并发读写，所以并不是线程安全的，使用多个 goroutine 对类型为 slice 的变量进行操作，每次输出的值大概率都不会一样，与预期值不一致; slice在并发执行中不会报错，但是数据会丢失

  * 解决办法
    * 方式一：通过加锁实现slice线程安全，适合对性能要求不高的场景。
    * 方式二：通过channel实现slice线程安全，适合对性能要求高的场景。

* 共享存储空间

  多个切片如果共享同一个底层数组，这种情况下，对其中一个切片或者底层数组的更改，会影响到其他切片





#### 2、常用操作

* 创建

* 增加

* 删除

  ```go
  func TestSliceDelete(t *testing.T) {
      slice1 := []int{1, 2, 3, 4, 5}
      var x int
      // 删除最后一个元素
      x, slice1 = slice1[len(slice1)-1], slice1[:len(slice1)-1] 
      t.Log(x, slice1, len(slice1), cap(slice1)) 
      // 5 [1 2 3 4] 4 5
  
      // 删除第2个元素    
      slice1 = append(slice1[:2], slice1[3:]...) 
      t.Log(slice1, len(slice1), cap(slice1))    
      // [1 2 4] 3 5
  }
  ```

  

* 查找

* 修改

* 截取

  ```
  // 截取 slice[left:right:max]
  ```

  

* 遍历

  ```go
  /**
  * 切片遍历
   */
  func TestSliceTravel(t *testing.T) {
      slice1 := []int{1, 2, 3, 4}
      for i := 0; i < len(slice1); i++ {
          t.Log(slice1[i])
      }
      for idx, e := range slice1 {
          t.Log(idx, e)
      }
      for _, e := range slice1 {
          t.Log(e)
      }
  }
  ```

  

* 反转

  ```go
  func TestSliceReverse(t *testing.T) {
      a := []int{1, 2, 3, 4, 5}
      for left, right := 0, len(a)-1; left < right; left, right = left+1, right-1 {
          a[left], a[right] = a[right], a[left]
      }
      t.Log(a, len(a), cap(a)) 
      // [5 4 3 2 1] 5 5
  }
  ```

  

* 拷贝

  * 深拷贝

    ```go
    拷贝的是数据本身，创造一个样的新对象，新创建的对象与原对象不共享内存，新创建的对象在内存中开辟一个新的内存地址，新对象值修改时不会影响原对象值。既然内存地址不同，释放内存地址时，可分别释放
    
    值类型的数据，默认赋值操作都是深拷贝，Array、Int、String、Struct、Float，Bool。引用类型的数据如果想实现深拷贝，需要通过辅助函数完成
    
    比如golang深拷贝copy 方法会把源切片值(即 from Slice )中的元素复制到目标切片(即 to Slice )中，并返回被复制的元素个数，copy 的两个类型必须一致。copy 方法最终的复制结果取决于较短的那个切片，当较短的切片复制完成，整个复制过程就全部完成了
    
    /**
    
    深拷贝
    */
    func TestSliceDeepCopy(t *testing.T) {
    slice1 := []int{1, 2, 3, 4, 5}
    slice2 := make([]int, 5, 5)
    // 深拷贝
    copy(slice2, slice1)
    t.Log(slice1, len(slice1), cap(slice1))
    // [1 2 3 4 5] 5 5
    t.Log(slice2, len(slice2), cap(slice2))
    // [1 2 3 4 5] 5 5
    slice1[1] = 100
    t.Log(slice1, len(slice1), cap(slice1))
    // [1 100 3 4 5] 5 5
    t.Log(slice2, len(slice2), cap(slice2))
    // [1 2 3 4 5] 5 5
    }
    ```

    

  * 浅拷贝

    ```go
    拷贝的是数据地址，只复制指向的对象的指针，此时新对象和老对象指向的内存地址是一样的，新对象值修改时老对象也会变化。释放内存地址时，同时释放内存地址。
    
    引用类型的数据，默认全部都是浅拷贝，Slice、Map等
    
    目的切片和源切片指向同一个底层数组，任何一个数组元素改变，都会同时影响两个数组。
    
    /**
    
    浅拷贝
    */
    func TestSliceShadowCopy(t *testing.T) {
    slice1 := []int{1, 2, 3, 4, 5}
    // 浅拷贝（注意 := 对于引用类型是浅拷贝，对于值类型是深拷贝）
    slice2 := slice1
    t.Logf("%p", slice1) // 0xc00001c120
    t.Logf("%p", slice2) // 0xc00001c120
    // 同时改变两个数组，这时就是浅拷贝，未扩容时，修改 slice1 的元素之后，slice2 的元素也会跟着修改
    slice1[0] = 10
    t.Log(slice1, len(slice1), cap(slice1))
    // [10 2 3 4 5] 5 5
    t.Log(slice2, len(slice2), cap(slice2))
    // [10 2 3 4 5] 5 5
    // 注意下：扩容后，slice1和slice2不再指向同一个数组，修改 slice1 的元素之后，slice2 的元素不会被修改了
    slice1 = append(slice1, 5, 6, 7, 8)
    slice1[0] = 11
    // 这里可以发现，slice1[0] 被修改为了 11, slice1[0] 还是10
    t.Log(slice1, len(slice1), cap(slice1))
    // [11 2 3 4 5 5 6 7 8] 9 10
    t.Log(slice2, len(slice2), cap(slice2))
    // [10 2 3 4 5] 5 5
    }
    ```

  

  

#### 3、扩容

  ```go
  扩容会发生在slice append的时候，当slice的cap不足以容纳新元素，就会进行扩容
  
  源码：https://github.com/golang/go/...
  
  func growslice(et *_type, old slice, cap int) slice {
        // 省略一些判断...
      newcap := old.cap
      doublecap := newcap + newcap
      if cap > doublecap {
          newcap = cap
      } else {
          if old.len < 1024 {
              newcap = doublecap
          } else {
              // Check 0 < newcap to detect overflow
              // and prevent an infinite loop.
              for 0 < newcap && newcap < cap {
                  newcap += newcap / 4
              }
              // Set newcap to the requested cap when
              // the newcap calculation overflowed.
              if newcap <= 0 {
                  newcap = cap
              }
          }
      }
      // 省略一些后续...
  }
  如果新申请容量比两倍原有容量大，那么扩容后容量大小 等于 新申请容量
  如果原有 slice 长度小于 1024， 那么每次就扩容为原来的 2 倍
  如果原 slice 大于等于 1024， 那么每次扩容就扩为原来的 1.25 倍
  ```

  

  

#### 4、内存泄露

  ```go
  由于slice的底层是数组，很可能数组很大，但slice所取的元素数量却很小，这就导致数组占用的绝大多数空间是被浪费的
  
  Case1:
  
  比如下面的代码，如果传入的slice b是很大的，然后引用很小部分给全局量a，那么b未被引用的部分（下标1之后的数据）就不会被释放，造成了所谓的内存泄漏。
  
  var a []int
  
  func test(b []int) {
      a = b[:1] // 和b共用一个底层数组
      return
  }
  那么只要全局量a在，b就不会被回收。
  
  如何避免？
  
  在这样的场景下注意：如果我们只用到一个slice的一小部分，那么底层的整个数组也将继续保存在内存当中。当这个底层数组很大，或者这样的场景很多时，可能会造成内存急剧增加，造成崩溃。
  
  所以在这样的场景下，我们可以将需要的分片复制到一个新的slice中去，减少内存的占用
  
  var a []int
  
  func test(b []int) {
      a = make([]int, 1)
      copy(a, b[:0])
      return
  }
  Case2:
  
  比如下面的代码，返回的slice是很小一部分，这样该函数退出后，原来那个体积较大的底层数组也无法被回收
  
  func test2() []int{
      s = make([]int, 0, 10000)
      for i := 0; i < 10000; i++ {
          s = append(s, p)
      }
      s2 := s[100:102]
      return s2
  }
  如何避免？
  
  将需要的分片复制到一个新的slice中去，减少内存的占用
  
  func test2() []int{
      s = make([]int, 0, 10000)
      for i := 0; i < 10000; i++ {
        // 一些计算...
          s = append(s, p)
      }
      s2 := make([]int, 2)
      copy(s2, s[100:102])
      return s2
  }
  ```

  

#### 5、切片与数组对比

  ```go
  数组是一个固定长度的，初始化时候必须要指定长度，不指定长度的话就是切片了
  
  数组是值类型，将一个数组赋值给另一个数组时，传递的是一份深拷贝，赋值和函数传参操作都会复制整个数组数据，会占用额外的内存；切片是引用类型，将一个切片赋值给另一个切片时，传递的是一份浅拷贝，赋值和函数传参操作只会复制len和cap，但底层共用同一个数组，不会占用额外的内存。
  
  //a是一个数组，注意数组是一个固定长度的，初始化时候必须要指定长度，不指定长度的话就是切片了
  a := [3]int{1, 2, 3}
  //b是数组，是a的一份深拷贝
  b := a
  //c是切片，是引用类型，底层数组是a
  c := a[:]
  for i := 0; i < len(a); i++ {
   a[i] = a[i] + 1
  }
  //改变a的值后，b是a的拷贝，b不变，c是引用，c的值改变
  fmt.Println(a) 
  //[2,3,4]
  fmt.Println(b) 
  //[1 2 3]
  fmt.Println(c) 
  //[2,3,4]
  //a是一个切片，不指定长度的话就是切片了
  a := []int{1, 2, 3}
  //b是切片，是a的一份拷贝
  b := a
  //c是切片，是引用类型
  c := a[:]
  for i := 0; i < len(a); i++ {
   a[i] = a[i] + 1
  }
  //改变a的值后，b是a的浅拷贝，b的值改派，c是引用，c的值改变
  fmt.Println(a) 
  //[2,3,4]
  fmt.Println(b) 
  //[2,3,4]
  fmt.Println(c) 
  //[2,3,4]
  ```

  

#### 6、实现原理

* array unsafe.Pointer
  * 64位系统 8字节、32位系统4字节
* len  int
  * 64位系统 8字节、32位系统4字节
* cap  int
  * 64位系统 8字节、32位系统4字节







## 三、map





## 四、channel





## 五、context

go1.7 加入标准库


context的作用就是在不同的goroutine之间同步请求特定的数据、取消信号以及处理请求的截止日期

上下文对于多个goroutine同时使用是安全的



#### 1、使用

* 创建根context

  ```go
  上面的两种方式是创建根context，不具备任何功能，具体实践还是要依靠context包提供的With系列函数来进行派生：
  
  func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
  func WithDeadline(parent Context, deadline time.Time) (Context, CancelFunc)
  func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
  func WithValue(parent Context, key, val interface{}) Context
  ```

  * context.Backgroud()

    ```
    context.Background是上下文的默认值，所有其他的上下文都应该从它衍生（Derived）出来
    
    
    所以在大多数情况下，我们都使用context.Background作为起始的上下文向下传递。
    ```

    

  * context.TODO()

    ```
    context.TODO 应该只在不确定应该使用哪种上下文时使用
    ```

* 子Context

  * WithValue携带数据
  * withTimeout超时控制
  * withCancel取消控制
  * 自定义Context





## 六、sync包

```
选择合适的锁
1、简单保护资源： 使用 sync.Mutex。
2、读多写少： 使用 sync.RWMutex。
3、单次初始化： 使用 sync.Once。
4、条件等待： 使用 sync.Cond。
5、并发安全的 Map： 使用 sync.Map。

在高并发场景下，也可以考虑无锁的方案，比如使用 Go 的原子操作（sync/atomic）或 Go 的 channel 来实现更高效的同步机制。
```



#### 1、sync.Mutex

sync.Mutex 是最常用的互斥锁，用于保护临界区，使同一时刻只有一个 goroutine 能访问共享资源。

特点

* 1、简单高效。
* 2、使用 Lock 和 Unlock 加锁解锁，使用不当可能导致死锁。




#### 2、sync.RWMutex

sync.RWMutex 是读写锁，允许多个 goroutine 同时进行读操作，但写操作是独占的

特点

* 1、适合读多写少的场景。
* 2、使用 RLock 和 RUnlock 进行读操作保护，Lock 和 Unlock 进行写操作保护



#### 3、sync.Once

sync.Once 用于确保某段代码只执行一次（如初始化操作），即使在并发环境下也是线程安全的

特点

* 确保初始化操作只执行一次。

* 常用于单例模式。

  ```
  package main
  
  import (
  "fmt"
  "sync"
  )
  
  func main() {
  	var once sync.Once
  	wg := sync.WaitGroup{}
  	wg.Add(3)
  
  	initialize := func() {
  		fmt.Println("Initialized")
  	}
  
  for i := 0; i < 3; i++ {
  	go func() {
  		defer wg.Done()
  		once.Do(initialize)
  	}()
  }
  
  wg.Wait()
  }
  ```

  



#### 4、sync.Cond

sync.Cond 是一个条件变量，用于协调 goroutine 等待或通知某些条件满足。

特点

* 适合复杂的条件同步。

* 需要结合锁（sync.Mutex 或 sync.RWMutex）使用。

  ```go
  package main
  
  import (
  "fmt"
  "sync"
  )
  
  func main() {
  cond := sync.NewCond(&sync.Mutex{})
  ready := false
  
  wg := sync.WaitGroup{}
  wg.Add(2)
  
  // Goroutine waiting for condition
  go func() {
  defer wg.Done()
  cond.L.Lock()
  for !ready {
  cond.Wait()
  }
  fmt.Println("Goroutine 1 is running")
  cond.L.Unlock()
  }()
  
  go func() {
  defer wg.Done()
  cond.L.Lock()
  fmt.Println("Goroutine 2 is preparing")
  ready = true
  cond.L.Unlock()
  cond.Signal() // Notify one waiting goroutine
  }()
  
  wg.Wait()
  }
  ```

  

#### 5、sync.Map

sync.Map 是一个线程安全的 Map，可以用于并发访问的场景，不需要显式加锁

特点

* 适用于频繁读写的共享数据。

* 提供线程安全的 Store、Load、Delete 等操作。

  ```go
  package main
  
  import (
  "fmt"
  "sync"
  )
  
  func main() {
  var sm sync.Map
  
  wg := sync.WaitGroup{}
  wg.Add(2)
  
  go func() {
  defer wg.Done()
  sm.Store("key1", "value1")
  }()
  
  go func() {
  defer wg.Done()
  sm.Store("key2", "value2")
  }()
  
  wg.Wait()
  
  sm.Range(func(key, value interface{}) bool {
  fmt.Println(key, ":", value)
  return true
  })
  }
  ```

  

#### 6、sync.WaitGroup

sync.WaitGroup 是 Go 标准库 sync 包中的一个类型，用于协同多个 goroutine 的结束。它可以确保主程序（或其他 goroutine）等待一组 goroutine 执行完成后再继续执行

```go
package main

import (
"fmt"
"sync"
"time"
)

func worker(id int, wg *sync.WaitGroup) {
defer wg.Done() // 在 goroutine 结束时将计数器减 1
fmt.Printf("Worker %d 开始工作\n", id)

time.Sleep(time.Second) // 模拟工作

fmt.Printf("Worker %d 完成工作\n", id)
}

func main() {
var wg sync.WaitGroup

for i := 1; i <= 5; i++ {
wg.Add(1) // 每启动一个 goroutine，计数器加 1
go worker(i, &wg)
}

wg.Wait() // 阻塞主程序，直到所有 goroutine 完成
fmt.Println("所有工作完成")
}
```



