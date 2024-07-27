这里先看一下Zinx最简单的Server雏形。



## 1. Zinx-V0.1-基础Server



为了更好的看到Zinx框架，首先Zinx构建Zinx的最基本的两个模块`ziface`和`znet`。



`ziface`主要是存放一些Zinx框架的全部模块的抽象层接口类，Zinx框架的最基本的是服务类接口`iserver`，定义在ziface模块中。



`znet`模块是zinx框架中网络相关功能的实现，所有网络相关模块都会定义在`znet`模块中。



### 1.1 Zinx-V0.1 代码实现



#### A) 创建zinx框架



在$GOPATH/src下创建`zinx`文件夹



#### B) 创建ziface、znet模块



在zinx/下 创建ziface、znet文件夹, 使当前的文件路径如下：



```bash
└── zinx
    ├── ziface
    │   └── 
    └── znet
        ├──
```



#### C) 在ziface下创建服务模块抽象层iserver.go



zinx/ziface/iserver.go



```go
package ziface

//定义服务器接口
type IServer interface{
    //启动服务器方法
    Start()
    //停止服务器方法
    Stop()
    //开启业务服务方法
    Serve()
}
```



#### D) 在znet下实现服务模块server.go



```go
package znet

import (
    "fmt"
    "net"
    "time"
    "zinx/ziface"
)

//iServer 接口实现，定义一个Server服务类
type Server struct {
    //服务器的名称
    Name string
    //tcp4 or other
    IPVersion string
    //服务绑定的IP地址
    IP string
    //服务绑定的端口
    Port int
}


//============== 实现 ziface.IServer 里的全部接口方法 ========

//开启网络服务
func (s *Server) Start() {
    fmt.Printf("[START] Server listenner at IP: %s, Port %d, is starting\n", s.IP, s.Port)

    //开启一个go去做服务端Linster业务
    go func() {
        //1 获取一个TCP的Addr
        addr, err := net.ResolveTCPAddr(s.IPVersion, fmt.Sprintf("%s:%d", s.IP, s.Port))
        if err != nil {
            fmt.Println("resolve tcp addr err: ", err)
            return
        }

        //2 监听服务器地址
        listenner, err:= net.ListenTCP(s.IPVersion, addr)
        if err != nil {
            fmt.Println("listen", s.IPVersion, "err", err)
            return
        }

        //已经监听成功
        fmt.Println("start Zinx server  ", s.Name, " succ, now listenning...")

        //3 启动server网络连接业务
        for {
            //3.1 阻塞等待客户端建立连接请求
            conn, err := listenner.AcceptTCP()
            if err != nil {
                fmt.Println("Accept err ", err)
                continue
            }

            //3.2 TODO Server.Start() 设置服务器最大连接控制,如果超过最大连接，那么则关闭此新的连接

            //3.3 TODO Server.Start() 处理该新连接请求的 业务 方法， 此时应该有 handler 和 conn是绑定的

            //我们这里暂时做一个最大512字节的回显服务
            go func () {
                //不断的循环从客户端获取数据
                for  {
                    buf := make([]byte, 512)
                    cnt, err := conn.Read(buf)
                    if err != nil {
                        fmt.Println("recv buf err ", err)
                        continue
                    }
                    //回显
                    if _, err := conn.Write(buf[:cnt]); err !=nil {
                        fmt.Println("write back buf err ", err)
                        continue
                    }
                }
            }()
        }
    }()
}

func (s *Server) Stop() {
    fmt.Println("[STOP] Zinx server , name " , s.Name)

    //TODO  Server.Stop() 将其他需要清理的连接信息或者其他信息 也要一并停止或者清理
}

func (s *Server) Serve() {
    s.Start()

    //TODO Server.Serve() 是否在启动服务的时候 还要处理其他的事情呢 可以在这里添加


    //阻塞,否则主Go退出， listenner的go将会退出
    for {
        time.Sleep(10*time.Second)
    }
}


/*
  创建一个服务器句柄
 */
func NewServer (name string) ziface.IServer {
    s:= &Server {
        Name :name,
        IPVersion:"tcp4",
        IP:"0.0.0.0",
        Port:7777,
    }

    return s
}
```



好了，以上我们已经完成了Zinx-V0.1的基本雏形了，虽然只是一个基本的回写客户端数据(我们之后会自定义处理客户端业务方法)，那么接下来我们就应该测试我们当前的zinx-V0.1是否可以使用了。



### 1.2 Zinx框架单元测试样例



理论上我们应该可以现在导入zinx框架，然后写一个服务端程序，再写一个客户端程序进行测试，但是我们可以通过Go的单元Test功能，进行单元测试



创建zinx/znet/server_test.go



```go
package znet

import (
	"fmt"
	"net"
	"testing"
	"time"
)

/*
	模拟客户端
 */
 func ClientTest() {

 	fmt.Println("Client Test ... start")
 	//3秒之后发起测试请求，给服务端开启服务的机会
 	time.Sleep(3 * time.Second)

 	conn,err := net.Dial("tcp", "127.0.0.1:7777")
 	if err != nil {
 		fmt.Println("client start err, exit!")
		return
	}

 	for {
 		_, err := conn.Write([]byte("hello ZINX"))
 		if err !=nil {
 			fmt.Println("write error err ", err)
 			return
		}

 		buf :=make([]byte, 512)
 		cnt, err := conn.Read(buf)
 		if err != nil {
 			fmt.Println("read buf error ")
 			return
		}

 		fmt.Printf(" server call back : %s, cnt = %d\n", buf,  cnt)

 		time.Sleep(1*time.Second)
	}
 }

//Server 模块的测试函数
func TestServer(t *testing.T) {

	/*
		服务端测试
	*/
	//1 创建一个server 句柄 s
	s := NewServer("[zinx V0.1]")

	/*
		客户端测试
	*/
	go ClientTest()

	//2 开启服务
	s.Serve()
}
```



在zinx/znet下执行



```bash
$ go test
```



执行结果，如下：



```bash
[START] Server listenner at IP: 0.0.0.0, Port 7777, is starting
Client Test ... start
listen tcp4 err listen tcp4 0.0.0.0:7777: bind: address already in use
 server call back : hello ZINX, cnt = 6
 server call back : hello ZINX, cnt = 6
 server call back : hello ZINX, cnt = 6
 server call back : hello ZINX, cnt = 6
```



说明我们的zinx框架已经可以使用了。



### 1.3 使用Zinx-V0.1完成应用程序



当然，如果感觉go test 好麻烦，那么我们可以完全基于zinx写两个应用程序，Server.go , Client.go



Server.go



```go
package main

import (
	"zinx/znet"
)

//Server 模块的测试函数
func main() {

	//1 创建一个server 句柄 s
	s := znet.NewServer("[zinx V0.1]")

	//2 开启服务
	s.Serve()
}
```



启动Server.go



```bash
go run Server.go
```



Client.go



```go
package main

import (
	"fmt"
	"net"
	"time"
)

func main() {

	fmt.Println("Client Test ... start")
	//3秒之后发起测试请求，给服务端开启服务的机会
	time.Sleep(3 * time.Second)

	conn,err := net.Dial("tcp", "127.0.0.1:7777")
	if err != nil {
		fmt.Println("client start err, exit!")
		return
	}

	for {
		_, err := conn.Write([]byte("hahaha"))
		if err !=nil {
			fmt.Println("write error err ", err)
			return
		}

		buf :=make([]byte, 512)
		cnt, err := conn.Read(buf)
		if err != nil {
			fmt.Println("read buf error ")
			return
		}

		fmt.Printf(" server call back : %s, cnt = %d\n", buf,  cnt)

		time.Sleep(1*time.Second)
	}
}
```



启动Client.go进行测试



```bash
go run Client.go
```



## 2.Zinx-V0.2-简单的连接封装与业务绑定 

​		 	 	 							 					

V0.1版本我们已经实现了了⼀一个基础的Server框架，现在我们需要对客户端链接和不不同的客户端链接所处 理理的不不同业务再做⼀一层接⼝口封装，当然我们先是把架构搭建起来。 					

现在在 ziface 下创建⼀一个属于链接的接⼝口⽂文件 iconnection.go ，当然他的实现⽂文件我们放在 znet 下的 connection.go 中。 

 									 					

### 2.1 Zinx-V0.2代码实现 

#### A) ziface创建iconnection.go 

zinx/ziface/iconnection.go 

```go
package ziface
import "net"

//定义连接接⼝口
type IConnection interface {
	//启动连接，让当前连接开始⼯工作
    Start()
	//停⽌止连接，结束当前连接状态M
	Stop()
	//从当前连接获取原始的socket TCPConn GetTCPConnection() *net.TCPConn //获取当前连接ID
	GetConnID() uint32 //获取远程客户端地址信息 RemoteAddr() net.Addr
}

//定义⼀一个统⼀一处理理链接业务的接⼝口
type HandFunc func(*net.TCPConn, []byte, int) error
```

​					 					

该接⼝口的⼀一些基础⽅方法，代码注释已经介绍的很清楚，这⾥里里先简单说明⼀一个HandFunc这个函数类型， 这个是所有conn链接在处理理业务的函数接⼝口，第⼀一参数是socket原⽣生链接，第⼆二个参数是客户端请求的 数据，第三个参数是客户端请求的数据⻓长度。这样，如果我们想要指定⼀一个conn的处理理业务，只要定义 ⼀一个HandFunc类型的函数，然后和该链接绑定就可以了了。 						 					

#### B) znet 创建iconnection.go 

zinx/znet/connection.go 

```go
package znet

import (
	"fmt"
	"net"
	"zinx/ziface"
)

type Connection struct {
	//当前连接的socket TCP套接字
	Conn *net.TCPConn
	//当前连接的ID 也可以称作为SessionID，ID全局唯一
	ConnID uint32
	//当前连接的关闭状态
	isClosed bool

	//该连接的处理方法api
	handleAPI ziface.HandFunc

	//告知该链接已经退出/停止的channel
	ExitBuffChan chan bool
}


//创建连接的方法
func NewConntion(conn *net.TCPConn, connID uint32, callback_api ziface.HandFunc) *Connection{
	c := &Connection{
		Conn:     conn,
		ConnID:   connID,
		isClosed: false,
		handleAPI: callback_api,
		ExitBuffChan: make(chan bool, 1),
	}

	return c
}

/* 处理conn读数据的Goroutine */
func (c *Connection) StartReader() {
	fmt.Println("Reader Goroutine is  running")
	defer fmt.Println(c.RemoteAddr().String(), " conn reader exit!")
	defer c.Stop()

	for  {
		//读取我们最大的数据到buf中
		buf := make([]byte, 512)
		cnt, err := c.Conn.Read(buf)
		if err != nil {
			fmt.Println("recv buf err ", err)
			c.ExitBuffChan <- true
			continue
		}
        //调用当前链接业务(这里执行的是当前conn的绑定的handle方法)
		if err := c.handleAPI(c.Conn, buf, cnt); err !=nil {
			fmt.Println("connID ", c.ConnID, " handle is error")
			c.ExitBuffChan <- true
			return
		}
	}
}

//启动连接，让当前连接开始工作
func (c *Connection) Start() {

	//开启处理该链接读取到客户端数据之后的请求业务
	go c.StartReader()

	for {
		select {
		case <- c.ExitBuffChan:
			//得到退出消息，不再阻塞
			return
		}
	}
}

//停止连接，结束当前连接状态M
func (c *Connection) Stop() {
	//1. 如果当前链接已经关闭
	if c.isClosed == true {
		return
	}
	c.isClosed = true

	//TODO Connection Stop() 如果用户注册了该链接的关闭回调业务，那么在此刻应该显示调用

	// 关闭socket链接
	c.Conn.Close()

	//通知从缓冲队列读数据的业务，该链接已经关闭
	c.ExitBuffChan <- true

	//关闭该链接全部管道
	close(c.ExitBuffChan)
}

//从当前连接获取原始的socket TCPConn
func (c *Connection) GetTCPConnection() *net.TCPConn {
	return c.Conn
}

//获取当前连接ID
func (c *Connection) GetConnID() uint32{
	return c.ConnID
}

//获取远程客户端地址信息
func (c *Connection) RemoteAddr() net.Addr {
	return c.Conn.RemoteAddr()
}
```

​					 					

#### C) 重新更更正⼀一下Server.go中 处理理conn的连接业务 

zinx/znet/server.go 

```go
package znet

import (
	"errors"
	"fmt"
	"net"
	"time"
	"zinx/ziface"
)

//iServer 接口实现，定义一个Server服务类
type Server struct {
	//服务器的名称
	Name string
	//tcp4 or other
	IPVersion string
	//服务绑定的IP地址
	IP string
	//服务绑定的端口
	Port int
}

//============== 定义当前客户端链接的handle api ===========
func CallBackToClient(conn *net.TCPConn, data []byte, cnt int) error {
	//回显业务
	fmt.Println("[Conn Handle] CallBackToClient ... ")
	if _, err := conn.Write(data[:cnt]); err !=nil {
		fmt.Println("write back buf err ", err)
		return errors.New("CallBackToClient error")
	}
	return nil
}

//============== 实现 ziface.IServer 里的全部接口方法 ========

//开启网络服务
func (s *Server) Start() {
	fmt.Printf("[START] Server listenner at IP: %s, Port %d, is starting\n", s.IP, s.Port)

	//开启一个go去做服务端Linster业务
	go func() {
		//1 获取一个TCP的Addr
		addr, err := net.ResolveTCPAddr(s.IPVersion, fmt.Sprintf("%s:%d", s.IP, s.Port))
		if err != nil {
			fmt.Println("resolve tcp addr err: ", err)
			return
		}

		//2 监听服务器地址
		listenner, err:= net.ListenTCP(s.IPVersion, addr)
		if err != nil {
			fmt.Println("listen", s.IPVersion, "err", err)
			return
		}

		//已经监听成功
		fmt.Println("start Zinx server  ", s.Name, " succ, now listenning...")

		//TODO server.go 应该有一个自动生成ID的方法
		var cid uint32
		cid = 0

		//3 启动server网络连接业务
		for {
			//3.1 阻塞等待客户端建立连接请求
			conn, err := listenner.AcceptTCP()
			if err != nil {
				fmt.Println("Accept err ", err)
				continue
			}

			//3.2 TODO Server.Start() 设置服务器最大连接控制,如果超过最大连接，那么则关闭此新的连接

			//3.3 处理该新连接请求的 业务 方法， 此时应该有 handler 和 conn是绑定的
			dealConn := NewConntion(conn, cid, CallBackToClient)
			cid ++

			//3.4 启动当前链接的处理业务
			go dealConn.Start()
		}
	}()
}

func (s *Server) Stop() {
	fmt.Println("[STOP] Zinx server , name " , s.Name)

	//TODO  Server.Stop() 将其他需要清理的连接信息或者其他信息 也要一并停止或者清理
}

func (s *Server) Serve() {
	s.Start()

	//TODO Server.Serve() 是否在启动服务的时候 还要处理其他的事情呢 可以在这里添加

	//阻塞,否则主Go退出， listenner的go将会退出
	for {
		time.Sleep(10*time.Second)
	}
}

/*
  创建一个服务器句柄
 */
func NewServer (name string) ziface.IServer {
	s:= &Server {
		Name :name,
		IPVersion:"tcp4",
		IP:"0.0.0.0",
		Port:7777,
	}

	return s
}
```

CallBackToClient 是我们给当前客户端conn对象绑定的handle⽅方法，当然⽬目前是server端强制绑定 的回显业务，我们之后会丰富框架，让这个⽤用户可以让⽤用户⾃自定义指定handle。 

在 start() ⽅方法中，我们主要做了了如下的修改: 

```go
			//3.3 处理该新连接请求的 业务 方法， 此时应该有 handler 和 conn是绑定的
			dealConn := NewConntion(conn, cid, CallBackToClient)
			cid ++

			//3.4 启动当前链接的处理业务
			go dealConn.Start()
```

好了，现在我们已经将connection的连接和handle绑定了，下⾯面我们在测试⼀一下Zinx-V0.2的框架是否 可以使⽤用吧。 

​					 					

### 2.2 使⽤用Zinx-V0.2完成应⽤用程序 



实际上，⽬目前Zinx框架的对外接⼝口并未改变，所以V0.1的测试依然有效。 

Server.go 

```go
package main

import (
	"zinx/znet"
)

//Server 模块的测试函数
func main() {

	//1 创建一个server 句柄 s
	s := znet.NewServer("[zinx V0.1]")

	//2 开启服务
	s.Serve()
}
```

启动Server.go

```go
go run Server.go
```

Client.go 

```go
package main

import (
	"fmt"
	"net"
	"time"
)

func main() {

	fmt.Println("Client Test ... start")
	//3秒之后发起测试请求，给服务端开启服务的机会
	time.Sleep(3 * time.Second)

	conn,err := net.Dial("tcp", "127.0.0.1:7777")
	if err != nil {
		fmt.Println("client start err, exit!")
		return
	}

	for {
		_, err := conn.Write([]byte("hahaha"))
		if err !=nil {
			fmt.Println("write error err ", err)
			return
		}

		buf :=make([]byte, 512)
		cnt, err := conn.Read(buf)
		if err != nil {
			fmt.Println("read buf error ")
			return
		}

		fmt.Printf(" server call back : %s, cnt = %d\n", buf,  cnt)

		time.Sleep(1*time.Second)
	}
}
```

启动Client.go进⾏行行测试 

```go
go run Client.go
```

现在我们已经简单初始了了Zinx的雏形，但是⽬目前离我们真正的框架还很远，接下来我们来改进zinx框 架。 

 				

 			