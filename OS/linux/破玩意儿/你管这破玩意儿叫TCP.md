**你是一台电脑，你的名字叫 A**

![img](image/1690952560314-300eb4e7-b760-4eec-894d-f84c4b9cfcae.png)

经过[《如果让你来设计网络》](http://mp.weixin.qq.com/s?__biz=Mzk0MjE3NDE0Ng==&mid=2247489907&idx=1&sn=a296cb42467cab6f0a7847be32f52dae&chksm=c2c663def5b1eac84b664c8c1cadf1c8ec23ea2e57e48e04add9b833c841256fc9449b62c0ec&scene=21#wechat_redirect)这篇文章中的一番折腾，只要你知道另一位伙伴 B 的 IP 地址，且你们之间的网络是通的，无论多远，你都可以将一个数据包发送给你的伙伴 B

![img](image/1690952560260-4a2dd6cb-72fc-4d6e-b4b0-ceb8b11c6ed4.png)

这就是物理层、数据链路层、网络层这三层所做的事情。

站在第四层的你，就可以不要脸地利用下三层所做的铺垫，随心所欲地发送数据，而不必担心找不到对方了。

![img](image/1690952560275-9da23e79-3e07-4e88-a100-e0085bc78caf.gif)

虽然你此时还什么都没干，但你还是给自己这一层起了个响亮的名字，叫做**传输层**。

你本以为自己所在的第四层万事大吉，啥事没有，但很快问题就接踵而至。

#    

**问题来了**

#   



前三层协议只能把数据包从一个主机搬到另外一台主机，但是，到了目的地以后，数据包具体交给哪个**程序**（进程）呢？

![img](image/1690952560324-bf1c4584-9985-42e7-9a9b-e6039424fd89.png)

所以，你需要把通信的进程区分开来，于是就给每个进程分配一个数字编号，你给它起了一个响亮的名字：**端口号**。

![img](image/1690952560299-2b19bcd8-73eb-4c2e-acdd-f25150d42191.png)

然后你在要发送的数据包上，增加了传输层的头部，**源端口号**与**目标端口号**。

![img](image/1690952563886-9dd34bfe-e9a1-49d1-bb74-17591a2352f4.png)

OK，这样你将原本主机到主机的通信，升级为了**进程和进程之间的通信**。

你没有意识到，你不知不觉实现了 **UDP 协议**！

（当然 UDP 协议中不光有源端口和目标端口，还有数据包长度和校验值，我们暂且略过）

就这样，你用 UDP 协议无忧无虑地同 B 进行着通信，一直没发生什么问题。

![img](image/1690952563891-0c44e24d-3a7d-4634-b6c9-e72530fc4ddf.gif)

但很快，你发现事情变得非常复杂......

#    

**丢包问题**

#   



由于网络的不可靠，数据包可能在半路丢失，而 A 和 B 却无法察觉。

![img](image/1690952563892-88d98bfb-0178-4b95-a3b2-b9f30e34c036.gif)

对于丢包问题，只要解决两个事就好了。

**第一个，A 怎么知道包丢了？**

答案：让 B 告诉 A

**第二个，丢了的包怎么办？**

答案：重传

于是你设计了如下方案，A 每发一个包，都必须收到来自 B 的**确认**（ACK），再发下一个，否则在一定时间内没有收到确认，就**重传**这个包。

![img](image/1690952563874-ff0fc978-320e-4532-954a-b48573b28227.gif)

你管它叫**停止等待协议**。只要按照这个协议来，虽然 A 无法保证 B 一定能收到包，但 A 能够确认 B 是否收到了包，收不到就重试，尽最大努力让这个通信过程变得可靠，于是你们现在的通信过程又有了一个新的特征，**可靠交付**。

#    

**效率问题**

#   



停止等待虽然能解决问题，但是效率太低了，A 原本可以在发完第一个数据包之后立刻开始发第二个数据包，但由于停止等待协议，A 必须等数据包到达了 B ，且 B 的 ACK 包又回到了 A，才可以继续发第二个数据包，这效率慢得可不是一点两点。

于是你对这个过程进行了改进，采用**流水线**的方式，不再傻傻地等。

![img](image/1690952564321-b18f9cee-e6e9-40f2-b637-8b543eee0009.gif)

#    

**顺序问题**

#   



但是网路是复杂的、不可靠的。

有的时候 A 发出去的数据包，分别走了不同的路由到达 B，可能无法保证和发送数据包时一样的顺序。

![img](image/1690952564386-5496bec1-9abf-4497-88c3-e027c5f9b345.gif)

在流水线中有多个数据包和ACK包在**乱序流动**，他们之间对应关系就乱掉了。

难道还回到停止等待协议？A 每收到一个包的确认（ACK）再发下一个包，那就根本不存在顺序问题。应该有更好的办法！

A 在发送的数据包中增加一个**序号**（seq），同时 B 要在 ACK 包上增加一个**确认号**（ack），这样不但解决了停止等待协议的效率问题，也通过这样标序号的方式解决了顺序问题。

![img](image/1690952564528-005ffe78-7362-4983-a02a-bc799844e7cb.gif)

而 B 这个确认号意味深长：比如 B 发了一个确认号为 ack = 3，它不仅仅表示 A 发送的序号为 2 的包收到了，还表示 2 之前的数据包都收到了。这种方式叫**累计确认**或**累计应答**。

![img](image/1690952564461-99cfe55c-e938-4230-9da5-dfcff15fb5be.gif)

注意，实际上 ack 的号是收到的最后一个数据包的序号 seq + 1，也就是告诉对方下一个应该发的序号是多少。但图中为了便于理解，ack 就表示收到的那个序号，不必纠结。

#    

**流量问题**

#   



有的时候，A 发送数据包的速度太快，而 B 的接收能力不够，但 B 却没有告知 A 这个情况。

![img](image/1690952564529-ca67affa-5c9f-4be9-91e8-d5596cec6993.gif)

怎么解决呢？

很简单，B 告诉 A 自己的接收能力，A 根据 B 的接收能力，相应控制自己的**发送速率**，就好了。

B 怎么告诉 A 呢？B 跟 A 说"我很强"这三个字么？那肯定不行，得有一个严谨的规范。

于是 B 决定，每次发送数据包给 A 时，顺带传过来一个值，叫**窗口大小**（win)，这个值就表示 B 的**接收能力**。同理，每次 A 给 B 发包时也带上自己的窗口大小，表示 A 的接收能力。

![img](image/1690952564926-8cfef323-e714-4bcd-a866-041bb4b3aeea.gif)

B 告诉了 A 自己的窗口大小值，A 怎么利用它去做 A 这边发包的流量控制呢？

很简单，假如 B 给 A 传过来的窗口大小 win = 5，那 A 根据这个值，把自己要发送的数据分成这么几类。

![img](image/1690952565053-c73c5bfc-a920-4d0e-af60-17b99e10c720.png)

图片过于清晰，就不再文字解释了。

当 A 不断发送数据包时，**已发送的最后一个序号**就往右移动，直到碰到了窗口的上边界，此时 A 就无法继续发包，达到了流量控制。

![img](image/1690952565132-81584d6b-7e8d-4cb4-9733-e15e19134123.gif)

但是当 A 不断发包的同时，A 也会收到来自 B 的确认包，此时**整个窗口**会往右移动，因此上边界也往右移动，A 就能发更多的数据包了。

![img](image/1690952565194-a97a9014-e2d4-4836-97a0-1f7805f4037e.gif)

以上都是在窗口大小不变的情况下，而 B 在发给 A 的 ACK 包中，每一个都可以**重新设置**一个新的窗口大小，如果 A 收到了一个新的窗口大小值，A 会随之调整。

如果 A 收到了比原窗口值更大的窗口大小，比如 win = 6，则 A 会直接将窗口上边界向右移动 1 个单位。

![img](image/1690952565398-7f34abb5-cf6c-432f-826a-a267b40bf857.gif)

如果 A 收到了比原窗口值小的窗口大小，比如 win = 4，则 A 暂时不会改变窗口大小，更不会将窗口上边界向左移动，而是等着 ACK 的到来，不断将左边界向右移动，直到窗口大小值收缩到新大小为止。

![img](image/1690952566156-7fffea98-e3a0-4071-a37f-b1f622854564.gif)

OK，终于将流量控制问题解决得差不多了，你看着上面一个个小动图，给这个窗口起了一个更生动的名字，**滑动窗口**。

#    

**拥塞问题**

#   



但有的时候，不是 B 的接受能力不够，而是网络不太好，造成了**网络拥塞**。

![img](image/1690952566372-c4d71a5c-9171-4628-bb90-acad617c734b.gif)

拥塞控制与流量控制有些像，但流量控制是受 B 的接收能力影响，而拥塞控制是受**网络环境**的影响。

拥塞控制的解决办法依然是通过设置一定的窗口大小，只不过，流量控制的窗口大小是 B 直接告诉 A 的，而拥塞控制的窗口大小按理说就应该是网络环境主动告诉 A。

但网络环境怎么可能主动告诉 A 呢？只能 A 单方面通过**试探**，不断感知网络环境的好坏，进而确定自己的拥塞窗口的大小。

![img](image/1690952566759-2c2be103-1fee-49b8-b9a9-d0a9c49861a7.gif)

拥塞窗口大小的计算有很多复杂的算法，就不在本文中展开了，假如**拥塞窗口的大小为  cwnd**，上一部分流量控制的**滑动窗口的大小为 rwnd**，那么窗口的右边界受这两个值共同的影响，需要取它俩的最小值。

**窗口大小 = min(cwnd, rwnd)**

含义很容易理解，当 B 的接受能力比较差时，即使网络非常通畅，A 也需要根据 B 的接收能力限制自己的发送窗口。当网络环境比较差时，即使 B 有很强的接收能力，A 也要根据网络的拥塞情况来限制自己的发送窗口。正所谓受其**短板**的影响嘛~

#    

**连接问题**

#   



有的时候，B 主机的相应进程还没有准备好或是挂掉了，A 就开始发送数据包，导致了浪费。

![img](image/1690952566753-5e13feaa-eff4-486f-a657-9fe793e590f3.gif)

这个问题在于，A 在跟 B 通信之前，没有事先确认 B 是否已经准备好，就开始发了一连串的信息。就好比你和另一个人打电话，你还没有"喂"一下确认对方有没有在听，你就巴拉巴拉说了一堆。

这个问题该怎么解决呢？

地球人都知道，**三次握手**嘛！

A：我准备好了(SYN)

B：我知道了(ACK)，我也准备好了(SYN)

A：我知道了(ACK)

![img](image/1690952566831-b4deaf4e-9fd1-4239-8b78-abf1dbdd7f00.gif)

A 与 B 各自在内存中维护着自己的状态变量，三次握手之后，双方的状态都变成了**连接已建立**（ESTABLISHED）。

虽然就只是发了三次数据包，并且在各自的内存中维护了状态变量，但这么说总觉得太 low，你看这个过程相当于双方建立连接的过程，于是你灵机一动，就叫它**面向连接**吧。

注意：这个连接是虚拟的，是由 A 和 B 这两个终端共同维护的，在网络中的设备根本就不知道连接这回事儿！

但凡事有始就有终，有了建立连接的过程，就要考虑释放连接的过程，又是地球人都知道，**四次挥手**嘛！

A：再见，我要关闭了(FIN)

B：我知道了(ACK)

给 B 一段时间把自己的事情处理完...

B：再见，我要关闭了(FIN)

A：我知道了(ACK)

![img](image/1690952567104-9465687b-0606-41de-b87c-b7101010bec5.gif)

#    

**总结**

#   



以上讲述的，就是 TCP 协议的核心思想，上面过程中需要传输的信息，就体现在 TCP 协议的头部，这里放上最常见的 TCP 协议头解读的图。

![img](image/1690952568183-126e14e6-4d1a-447d-a5cd-99246e94baa5.png)

不知道你现在再看下面这句话，是否能理解：

**TCP 是**

**面向连接的、可靠的、基于字节流的**

**传输层通信协议**

面向连接、可靠，这两个词通过上面的讲述很容易理解，那什么叫做基于字节流呢？

很简单，TCP 在建立连接时，需要告诉对方 MSS（最大报文段大小）。

也就是说，如果要发送的数据很大，在 TCP 层是需要按照 MSS 来切割成一个个的 **TCP 报文段** 的。

切割的时候我才不管你原来的数据表示什么意思，需要在哪里断句啥的，我就把它当成一串毫无意义的字节，在我想要切割的地方咔嚓就来一刀，标上序号，只要接收方再根据这个序号拼成最终想要的完整数据就行了。

在我 TCP 传输这里，我就把它当做一个个的**字节**，也就是基于字节流的含义了。

![img](image/1690952568444-8402a937-5b87-41d1-87a2-060924a113a9.jpeg)

最后留给大家一个作业，模拟 A 与 B 建立一个 TCP 连接。

**第一题**：A 给 B 发送 "aaa" ，然后 B 给 A 回复一个简单的字符串 "success"，并将此过程抓包。

**第二题**：A 给 B 发送 "aaaaaa ... a" 超过最大报文段大小，然后 B 给 A 回复一个简单的字符串 "success"，并将此过程抓包。

下面是我抓的包（第二题）

**三次握手阶段**

A -> B [SYN] Seq=0 Win=64240 Len=0

MSS=1460 WS=256

B - >A [SYN, ACK] Seq=0 Ack=1 Win=29200 Len=0

MSS=1424 WS=512

A -> B [ACK] Seq=1 Ack=1 Win=132352 Len=0

**数据发送阶段**

A -> B [ACK] Seq=1 Ack=1 Win=132352 Len=1424

A -> B [ACK] Seq=1425 Ack=1 Win=132352 Len=1424

A -> B [PSH, ACK] Seq=2849 Ack=1 Win=132352 Len=1247

B -> A [ACK] Seq=1 Ack=1425 Win=32256 Len=0

B -> A [ACK] Seq=1 Ack=2849 Win=35328 Len=0

B -> A [ACK] Seq=1 Ack=4096 Win=37888 Len=0

B -> A [PSH, ACK] Seq=1 Ack=4096 Win=37888 Len=7

**四次挥手阶段**

B -> A [FIN, ACK] Seq=8 Ack=4096 Win=37888 Len=0

A -> B [ACK] Seq=4096 Ack=9 Win=132352 Len=0

A -> B [FIN, ACK] Seq=4096 Ack=9 Win=132352 Len=0（下面少复制了一行ACK，抱歉）

详细的抓包数据与分析整理，就不在文章里展示了，可以在公众号 低并发编程 后台回复 TCP 获取。



**后记**









一提到 TCP，可能很多人都想起被三次握手和四次挥手所支配的恐惧。但其实你跟着文中的思路你就会发现，三次握手与四次挥手只占 TCP 所解决的核心问题中很小的一部分，只是因为它在面试中很适合作为知识点进行考察，所以在很多人的印象中就好像 TCP 的核心就是握手和挥手似的。

本文希望你能从问题出发，真正理解 TCP 所想要解决的问题，你会发现很多原理就好像生活常识一样顺其自然，并不复杂，希望你有收获～