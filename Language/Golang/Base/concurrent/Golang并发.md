# Golang并发

## 一、线程调度

操作系统线程调度是管理多个线程执行顺序的关键机制，其内部实现涉及多个方面，包括线程的创建、切换、优先级处理、以及资源管理等。具体实现因操作系统的不同而有所差异，比如Linux、Windows、``macOS``等都有各自的线程调度算法和策略。以下是操作系统线程调度内部实现的核心要素：

### 1. 线程调度的基本概念

线程调度的目的是决定在多任务系统中，哪个线程应该在下一个时间段内占用CPU。操作系统的内核会根据线程的优先级、状态、时间片等信息做出调度决策。

- **线程**：线程是比进程更小的调度单位。线程共享进程的资源，但每个线程有自己的堆栈和程序计数器。
- **调度策略**：调度器根据一定的策略选择哪个线程执行，常见的策略包括先到先服务（``FCFS``）、最短作业优先（SJF）、时间片轮转（RR）、优先级调度等。

### 2. 线程的状态

线程的调度依赖于线程的状态转换，常见的线程状态有：

- **就绪（Ready）**：线程已经准备好执行，等待CPU的分配。
- **运行中（Running）**：线程正在占用CPU执行。
- **阻塞（Blocked）**：线程因为等待某些资源（例如I/O操作或锁）而不能继续执行。
- **结束（Terminated）**：线程执行完毕，进入结束状态。

### 3. 线程调度器

线程调度器是操作系统内核的一部分，负责管理线程的执行。调度器的主要功能包括：

- **选择下一个要运行的线程**：根据线程的状态、优先级、以及系统的调度算法，决定哪个线程在下一个时间片内执行。
- **时间片管理**：大多数操作系统使用时间片（Time Slice）来分配CPU时间给线程。当时间片耗尽时，调度器会强制进行线程切换。
- **上下文切换（Context Switch）**：线程切换的核心是上下文切换，它涉及保存当前线程的状态（寄存器、堆栈指针、程序计数器等），并恢复下一个线程的状态。

### 4. 调度算法

不同操作系统使用不同的调度算法来管理线程调度，常见的有以下几种：

(1) **时间片轮转调度（Round-Robin, RR）**

- **工作原理**：将每个线程分配一个固定的时间片（时间量），当时间片用完后，如果线程还未完成，则将其移至就绪队列尾部，等待下次调度。
- **特点**：简单且公平，但不考虑线程的优先级和工作量。

(2) **优先级调度**

- **工作原理**：每个线程被赋予一个优先级，调度器优先选择优先级最高的线程运行。优先级可能是静态的（不变）或动态的（随系统状态变化）。
- **特点**：响应速度快，但可能导致低优先级线程长时间得不到执行（即优先级反转问题）。

(3) **多级反馈队列（Multilevel Feedback Queue, ``MLFQ``）**

- **工作原理**：使用多个队列，不同队列有不同的优先级。新的线程进入高优先级队列，如果未能完成，在后续时间片中会被降到较低优先级的队列中。
- **特点**：结合了优先级调度和时间片调度的优点，适用于多种工作负载。

(4) **最短剩余时间优先（Shortest Remaining Time First, ``SRTF``）**

- **工作原理**：每次调度选择剩余执行时间最短的线程运行。这是一种抢占式算法。
- **特点**：最小化平均等待时间，但需要准确的估算执行时间。

### 5. 实时调度算法

对于实时操作系统（``RTOS``），调度策略要求能够满足线程的实时性需求，确保线程能在指定的时间内执行。常见的算法有：

- **固定优先级调度**（Rate-Monotonic Scheduling, RMS）
- **最早截止时间优先**（Earliest Deadline First, ``EDF``）

### 6. 内核态与用户态切换

线程调度器运行在内核态，当发生线程调度时，系统会从用户态切换到内核态。用户态切换到内核态的常见情况包括：

- **系统调用**：用户进程发起的系统调用会导致进入内核态。
- **硬件中断**：例如时钟中断用于时间片轮转调度。
- **异常处理**：例如页面错误（Page Fault）。

上下文切换开销较大，因为需要保存和恢复线程的状态，所以操作系统会尽量减少不必要的上下文切换。

### 7. Linux中的线程调度

以Linux为例，Linux内核采用的是**完全公平调度器（Completely Fair Scheduler, ``CFS``）**。``CFS``的目标是将CPU时间尽可能公平地分配给所有线程。

- **红黑树（RB-Tree）**：``CFS``使用红黑树来组织就绪线程。红黑树是一种自平衡二叉搜索树，能够快速查找最“饥饿”的线程。
- **虚拟运行时间**：每个线程都有一个虚拟运行时间，用来衡量其获得的CPU时间，``CFS``通过比较虚拟运行时间来决定调度顺序。

### 8. Windows中的线程调度

Windows使用的是基于优先级的抢占式调度机制，称为**Windows Scheduler**。线程有动态优先级和静态优先级，调度器会根据优先级选择线程运行。

- **线程池和工作队列**：Windows中还提供了线程池和工作队列机制来优化线程的管理和调度。
- **实时优先级类**：对于关键性任务，Windows提供了更高的优先级类，确保实时任务能够被及时调度。

### 9. 多核处理器调度

在多核处理器系统中，线程调度器不仅要决定哪个线程运行，还要决定在哪个处理器核心上运行。常见的多核调度策略包括：

- **负载均衡**：确保所有核心的负载均衡，避免某些核心过载而其他核心闲置。
- **处理器亲和性**：尽量让线程在上次运行的核心上继续执行，以减少缓存失效。

### 10. 总结

操作系统线程调度的内部实现是一个复杂的过程，涉及线程的状态管理、优先级处理、上下文切换、时间片管理、多核调度等多个方面。不同的操作系统采用了不同的调度策略，以优化线程的执行效率、响应时间和公平性。





## 二、CPU核心与线程

CPU核心与线程的关系紧密相关，理解这两者的交互对于优化多线程程序的执行性能非常重要。以下是CPU核心和线程之间关系的详细解释：

### 1. **CPU核心的概念**

- **核心（Core）**：CPU核心是处理器内部的一个计算单元。现代的多核CPU包含多个核心，每个核心可以独立执行程序指令。一个物理核心能够处理一个或多个线程，视系统架构而定。
- **多核CPU**：具有多个核心的处理器可以同时处理多个任务。例如，一个四核处理器可以同时运行四个独立的线程，理论上每个核心可以独立处理一个任务。

### 2. **线程的概念**

- **线程**：线程是比进程更小的执行单元，是程序的实际运行实例。线程共享进程的地址空间和资源（如内存、文件句柄），但每个线程有独立的执行路径、堆栈、和寄存器。
- **多线程**：在多线程程序中，多个线程可以并发执行。这些线程可以在多个核心上并行运行，也可以在单个核心上通过时间片轮转来模拟并行执行。

### 3. **CPU核心与线程的关系**

- **并行执行**：在多核处理器中，每个核心可以独立运行一个线程。如果有多个核心和多个线程，它们可以在不同的核心上同时执行，实现真正的并行性。例如，如果一个系统有4个核心和4个线程，每个核心都可以运行一个线程，所有线程可以在同一时间并行执行。
- **并发执行**：在单核处理器或多线程多于核心数的情况下，系统通过时间片轮转的方式在不同线程之间切换，从而实现**并发**。这意味着虽然看起来多个线程在同时执行，但实际上每个时刻只有一个线程在核心上执行，线程快速切换以模拟并行。
- **处理器亲和性**：线程调度器可以将某个线程固定到特定的核心上执行，这称为**处理器亲和性（Processor Affinity）**。这样可以减少线程在多个核心之间切换时的上下文切换开销，保持缓存的一致性。

### 4. **硬件线程（超线程）**

- **超线程（Hyper-Threading）**：Intel的超线程技术允许每个物理核心模拟成两个逻辑核心（硬件线程）。每个逻辑核心可以运行一个线程，理论上提升了并行处理能力，但实际性能提升依赖于工作负载的性质。并非所有类型的程序都能从超线程中获益。

  例如，一个4核的Intel CPU，如果支持超线程，会表现为拥有8个逻辑核心。这样，它可以同时调度最多8个线程，每对逻辑核心共享一个物理核心的资源。

### 5. **线程调度与核心的关系**

- **操作系统线程调度器**：操作系统负责将线程分配到CPU核心上运行。调度器会根据系统负载、核心可用性、线程优先级等因素进行调度。在线程数量多于核心数量的情况下，调度器会通过时间片轮转机制在多个线程之间切换。
- **负载均衡**：在多核系统中，调度器通常会尝试将负载均匀地分布在所有核心上，避免某些核心过载而其他核心闲置。例如，Linux内核的调度器会定期平衡各核心之间的负载。

### 6. **实际执行中的场景**

- **单线程程序**：在单线程程序中，只有一个线程会被创建并执行。无论CPU有多少个核心，只有一个核心会被使用，除非程序生成了更多的线程或进程。
- **多线程程序**：在多线程程序中，如果CPU有多个核心，操作系统可以将多个线程分配到不同的核心上，从而实现真正的并行执行。比如，在一个8核心的CPU上，8个线程可以同时执行，每个线程在一个独立的核心上运行。
- **超线程 vs 实体核心**：超线程技术虽然可以在每个核心上运行多个线程，但每个逻辑核心共享一个物理核心的资源，所以其性能通常不如真正的物理核心高效。因此，超线程更多是一种通过提高核心利用率来提升性能的技术，而不是直接增加核心数量。

### 7. **性能优化与线程数**

在实际应用中，线程数与核心数的关系直接影响性能：

- **合适的线程数**：在多核系统中，理想的线程数通常接近于可用的核心数，以确保每个核心有工作负载，而不会出现资源竞争或大量的上下文切换。
- **过多的线程**：如果线程数远超可用核心数，系统将频繁地进行线程上下文切换，这会导致性能下降，因为上下文切换有一定的开销。
- **I/O密集型 vs 计算密集型**：对于I/O密集型任务，线程数可以多于核心数，因为线程会因为等待I/O操作而频繁阻塞；而对于计算密集型任务，线程数一般应接近核心数，以最大化核心的利用率。

### 8. **总结**

- **CPU核心**是处理线程的物理计算单元。每个核心可以同时运行一个或多个线程（如果支持超线程）。
- **线程**是程序的最小执行单元，可以在单个核心上通过并发执行或在多个核心上实现真正的并行执行。
- 在多核处理器上，多个线程可以并行执行，而在单核处理器上，线程通过时间片轮转并发执行。
- 操作系统的调度器负责将线程分配到核心上运行，调度的策略和效率对系统性能有重要影响。

通过合理设计多线程程序，充分利用CPU核心的计算能力，可以显著提升程序的执行效率。







## 三、线程与协程

线程与协程在调度机制上的区别反映了它们在操作系统和用户级别的不同管理方式。以下是两者在调度上的关键区别：

### 1. **线程调度**

**线程的特点**

- **由操作系统管理**：线程是操作系统的调度单位，由操作系统内核的调度器（如Linux的CFS调度器）负责管理。线程的创建、销毁、状态切换和上下文切换都由操作系统完成。
- **并发执行**：线程可以并发地执行在不同的CPU核心上（多核CPU），也可以在单核CPU上通过时间片轮转并发执行。

**线程调度的特点**

- **抢占式调度**：线程的调度通常是抢占式的，即操作系统可以随时中断一个正在运行的线程，切换到另一个线程。这种抢占式调度通常由时钟中断驱动，当时间片用尽时，操作系统会强制进行上下文切换。
- **内核态切换**：线程调度依赖于操作系统内核，因此线程的上下文切换涉及内核态与用户态的切换，这会带来一定的性能开销。每次切换时，操作系统需要保存当前线程的状态（寄存器、堆栈等），并恢复即将执行线程的状态。
- **多核利用**：由于线程是由操作系统管理的，操作系统可以利用多核CPU的并行性，将多个线程分布到不同的核心上执行，从而实现真正的并行。

**调度器控制**：

- 操作系统的调度器（如Linux、Windows等）根据线程的优先级、状态、时间片等参数来决定调度顺序。线程的调度是全自动的，开发者不需要显式地管理调度逻辑。

### 2. **协程调度**

**协程的特点**

- **用户级调度**：协程（Coroutine）是用户态的轻量级执行单元，不依赖于操作系统的内核调度器。协程的切换和调度完全在用户态进行，由程序员或协程库自行管理。
- **合作式调度**：协程的调度通常是合作式的，即协程在执行时会主动让出控制权。协程自己决定何时暂停（通过`yield`或`await`等机制），将执行权交给其他协程。这意味着调度的控制权在协程手中，而不是操作系统。

**协程调度的特点**

- **手动切换**：协程的上下文切换由用户代码控制，没有操作系统的介入。因此协程的切换开销非常小，不涉及内核态切换，只需要在用户态中保存和恢复必要的状态（如程序计数器和堆栈指针）。
- **单线程环境**：协程通常运行在单线程环境中，它们在一个线程内部实现并发。虽然它们能表现出并发性，但并不是真正的并行，因为同一时刻只有一个协程在运行。
- **非抢占式**：协程的调度是非抢占式的，意味着一个协程必须显式地让出执行权，其他协程才能运行。如果一个协程不让出控制权，整个程序的其他协程将会被阻塞。

**调度器控制**：

- 协程的调度通常由用户态的协程库（如Python的`asyncio`，Go语言的goroutine调度器）管理。协程调度器按照程序逻辑或事件循环来安排协程的执行顺序。

### 3. **线程与协程调度的对比**

| 特性           | 线程调度                         | 协程调度                         |
| -------------- | -------------------------------- | -------------------------------- |
| **管理方**     | 操作系统内核管理                 | 用户态库或程序员管理             |
| **调度方式**   | 抢占式调度                       | 合作式调度                       |
| **切换开销**   | 高（涉及内核态和用户态切换）     | 低（仅在用户态切换）             |
| **并发性**     | 真正并发（可以在多核上并行执行） | 伪并发（单线程中通过切换执行）   |
| **上下文切换** | 操作系统自动进行                 | 由用户代码显式管理               |
| **多核利用**   | 支持多核并行                     | 通常单线程（除非配合多线程使用） |
| **阻塞行为**   | 阻塞会影响整个线程的执行         | 可以通过异步I/O避免阻塞整个程序  |
| **控制权**     | 操作系统控制                     | 协程本身控制                     |

### 4. **应用场景**

- **线程适用场景**：
  - 需要充分利用多核CPU的并行能力，例如在多核服务器上运行高并发、多任务的系统。
  - I/O和计算密集型任务都适合线程调度，操作系统的调度器可以管理这些任务的优先级和执行时间。
- **协程适用场景**：
  - 适用于I/O密集型应用，例如网络服务、数据库请求等，因为协程可以在等待I/O的同时执行其他任务，避免阻塞整个程序。
  - 协程可以用在轻量级并发的场景中，比如需要管理大量并发任务但不需要多核并行的环境。

### 5. **总结**

- **线程**由操作系统调度，适合需要并行处理的大型程序，尤其是在多核系统上。它的调度是抢占式的，操作系统会根据时间片或优先级自动切换线程，但上下文切换开销较大。
- **协程**由用户态管理，适合轻量级并发和I/O密集型任务。它的调度是合作式的，切换开销很小，但同一时刻只能有一个协程在执行，且多依赖手动控制让出执行权。

线程适合需要利用多核CPU并行能力的任务，而协程更适合单线程中需要高效管理大量并发任务的场景。





## 四、线程与golang协程

Go语言中的协程（称为**goroutine**）与传统的线程在调度上的区别主要体现在调度机制、执行方式和系统资源管理上。Go的**goroutine**调度采用了一种独特的方式，旨在优化并发编程的性能。以下是Go协程和传统线程调度的区别：

### 1. **线程调度**

**传统线程的特点**

- **内核级线程**：线程是由操作系统管理的，通常是内核级的调度单位。操作系统的调度器决定哪个线程在何时运行，并通过抢占式调度（preemptive scheduling）管理多个线程。
- **并行执行**：操作系统线程可以在多个CPU核心上同时并行执行，线程调度由操作系统的调度器处理。操作系统可以将不同线程分配到不同的核心上，从而实现并行性。
- **上下文切换**：线程的上下文切换需要保存和恢复大量的状态信息（如寄存器、堆栈指针等），并且涉及从用户态切换到内核态。这种上下文切换开销较大，频繁的切换可能会影响系统性能。
- **内存开销**：每个线程通常都有自己的栈内存空间（例如，几MB大小），这意味着在创建大量线程时，内存消耗会迅速增加。

### 2. **Goroutine调度**

**Goroutine的特点**

- **用户级调度**：Goroutine是Go语言中实现的轻量级用户级线程，由Go语言的**运行时调度器**管理，而不是由操作系统的内核调度器管理。它们比操作系统线程更加轻量，调度更加高效。
- **高效的并发**：Goroutine的栈大小非常小，初始值通常只有几KB，并且可以动态扩展。这使得创建大量goroutine比创建大量线程更加高效，尤其适用于大规模并发场景。
- **协作式调度和抢占式调度结合**：最初，goroutine主要依赖于协作式调度，即goroutine在需要时显式地让出执行权。然而，在Go 1.14版本之后，Go增加了**抢占式调度**的能力，当某个goroutine运行时间过长时，Go运行时系统可以强制切换到其他goroutine，避免阻塞整个调度系统。

**Go的M调度模型**

- Go语言使用了一个M调度模型，即将M个goroutine映射到N个操作系统线程上。这种模型通过一个用户态的调度器管理goroutine在操作系统线程上的执行。

  - **M (Goroutine)**：这是Go中的轻量级协程，每个goroutine是一个独立的执行单元，可以由用户级调度器控制。
  - **N (OS Thread)**：Go运行时将多个goroutine分配到少量的操作系统线程上运行，操作系统线程数量远小于goroutine的数量。
  
- **P (Processor)**：Go的调度模型引入了一个抽象的概念叫做**P (Processor)**，它代表了一个执行goroutine的执行环境。P的数量决定了可以同时活跃运行的goroutine的数量（类似于最大并行数）。每个P与一个OS线程绑定，调度器会将goroutine分配给这些P进行执行。

### 3. **调度过程比较**

**线程调度**

- **操作系统负责调度**：传统的操作系统线程调度是由操作系统内核完成的，基于抢占式调度，线程可以在任何时候被内核中断并切换到另一个线程。
- **时间片轮转**：线程调度依赖时间片，当时间片耗尽时，操作系统会强制线程让出CPU。这种调度方式可以在多核环境下实现真正的并行，但上下文切换的开销较大。
- **适用于多核并行**：由于线程调度是操作系统级的，线程可以分布在多个CPU核心上执行，利用多核系统的并行计算能力。

**Goroutine调度**

- **Go运行时调度器负责调度**：goroutine的调度是由Go语言的运行时调度器（用户级调度器）管理的。该调度器在用户态中运行，因此上下文切换的开销远小于线程切换。
- **协作与抢占结合**：goroutine调度最初是协作式的，意味着一个goroutine必须显式让出执行权给其他goroutine。但自Go 1.14起，增加了抢占式调度支持，运行时调度器可以在合适的时机抢占运行时间过长的goroutine。
- **基于P的模型**：Go的调度器会将多个goroutine分配到有限的P上执行。P可以被看作是执行goroutine的上下文，多个goroutine被循环分配到这些P上，并通过底层的N个OS线程执行。调度器负责平衡P与OS线程之间的负载。
- **任务窃取（Work Stealing）**：Go调度器采用了任务窃取算法，当某个P中的goroutine队列耗尽时，它会尝试从其他P的队列中窃取任务，从而保持CPU核心的高效利用。

### 4. **性能与资源管理**

- **上下文切换开销**：由于goroutine的调度发生在用户态，而不涉及用户态与内核态的切换，因此其上下文切换的开销要远小于操作系统线程的切换。goroutine的栈可以动态扩展，初始栈大小非常小，意味着可以轻松创建数以万计的goroutine而不会耗尽内存资源。

- **并发与并行**：虽然goroutine可以在单个线程内并发执行，但通过Go的M

  

  模型，它们可以在多个操作系统线程上分布执行，从而在多核CPU上实现并行。goroutine的调度器自动管理这一过程，开发者无需关心底层的线程管理。

  

- **资源消耗**：创建一个操作系统线程通常需要较多的资源，如几MB的栈内存和内核管理的控制块，而创建一个goroutine的开销极小，通常只需要几KB的栈空间，并且栈可以动态扩展。因此，Go语言更适合处理大规模并发任务。

### 5. **应用场景对比**

- 传统线程：

  - 适用于需要并行处理、充分利用多核CPU的场景，尤其是在计算密集型任务中。
  - 更适合直接与操作系统交互的低级并发操作，例如需要细粒度控制线程的生命周期、优先级和调度策略的应用。

- Go语言的goroutine：

  - 更适合处理大量并发任务的场景，尤其是I/O密集型应用（如网络服务、Web服务器等），可以在单个进程中高效管理数以万计的并发任务。
  - 由于goroutine轻量且调度效率高，适用于构建高并发的应用程序，如微服务架构中的并发处理、协作任务和事件驱动模型等。

### 6. **总结**

- **传统线程**依赖操作系统内核进行调度，具有抢占式调度和多核并行的优势，但上下文切换开销较大，资源消耗较多。

- **Goroutine**则是用户态的轻量级协程，由Go运行时调度器管理，调度开销非常小，可以高效地处理大量并发任务。通过M

  

  调度模型，Go语言的调度器将大量goroutine映射到较少的操作系统线程上，极大地优化了资源使用。

  

Go的goroutine设计极大地简化了并发编程，结合了协作式与抢占式调度的优点，提供了高效的并发处理能力，而不需要开发者手动管理线程的复杂性。





## 五、Golang 调度模型

Golang 的 GMP 模型是 Go 语言运行时的核心调度模型，用于管理 Goroutine 的执行。GMP 模型的设计目标是通过高效的调度机制来充分利用多核处理器的并行性，从而提高程序的性能和并发性。以下是对 GMP 模型的详细说明：

### 1、GMP 模型的基本组成部分

- **G (Goroutine)**：Goroutine 是 Go 语言中的轻量级线程。它由 Go 运行时调度，是程序中执行逻辑的基本单位。每个 Goroutine 都包含一个栈、程序计数器（PC）等执行上下文，但它们比操作系统线程更轻量，能够在较低的资源消耗下创建成千上万个。
- **M (Machine/OS Thread)**：M 表示操作系统线程。M 负责真正执行 Goroutine 的代码。一个 M 可以执行多个 G，M 是 Go 运行时与操作系统之间的桥梁。M 代表了系统层的执行单元。
  - 概念：在 Go 的 GMP 模型中，M（Machine/Thread）代表操作系统线程
  - 创建
    - 初始创建：当 Go 程序启动时，Go 运行时会创建若干 M，并与 P（Processor）绑定，这些 M 是 Goroutine 的初始执行载体
    - 动态增加:
      - 当有新的 Goroutine 创建时，P 会尝试调度它。如果 P 当前没有可用的 M，则 Go 运行时会创建一个新的 M 来处理新的 Goroutine
      - 当 Goroutine 的数量超过当前可用的 M 时，Go 运行时会动态创建新的 M，以保证足够的线程来执行这些 Goroutine
      - 如果某个 M 正在执行的 Goroutine 进行了一次阻塞的系统调用（如文件 I/O、网络 I/O），这个 M 会被阻塞在该系统调用上，无法继续执行其他 Goroutine。为了防止整个程序因为一个阻塞的 M 而停滞，Go 运行时会创建新的 M，并将该 M 与空闲的 P 绑定，以继续执行其他 Goroutine。

  - 数量
    - Go 语言中，M 的数量并没有明确的硬性限制。操作系统线程的上限通常取决于操作系统的资源和配置（例如系统的内存、线程管理机制等），而不是 Go 语言自身的限制

- **P (Processor/Processor Context)**：P 表示处理器，包含 Goroutine 的本地队列和调度上下文。P 是 G 和 M 之间的调度器，每个 P 都与一个 M 绑定，M 只有获取了 P 才能执行 G。P 的数量通过环境变量 `GOMAXPROCS` 控制，默认情况下等于 CPU 核心数。
  - 概念
    - P 代表处理器，它不是物理的 CPU，而是 Go 的一个调度逻辑概念，P 负责调度 M 执行 Goroutine，管理 Goroutine 队列、运行时栈、任务分配等。在每个时刻，P 会和一个 M 关联，执行一组 Goroutine

  - 创建
    - 启动程序时创建

  - 数量
    - 默认数量：Go 运行时默认创建的 P 数量等于 CPU 的核心数
    - 设置数量：可以使用 runtime.GOMAXPROCS(n) 来手动设置 P 的数量


### 2、GMP 模型的工作机制

1. **调度器初始化**：Go 运行时根据 `GOMAXPROCS` 参数创建一定数量的 P，并且为每个 P 分配 Goroutine 的本地队列。
2. **Goroutine 创建**：用户通过 `go` 关键字创建新的 Goroutine，这些 Goroutine 会被放入当前 P 的本地队列中，等待调度器调度执行。
3. **Goroutine 调度**：
   - 调度器从 P 的本地队列中选取一个 Goroutine（G），然后把 G 分配给绑定的 M 执行。
   - 如果 M 上没有可以执行的 Goroutine，M 可能会从全局队列或其他 P 的队列中窃取任务。
   - 如果 P 的本地队列满了，P 会将一些 Goroutine 推送到全局队列，供其他 P 使用。
4. **G 和 M 的关联**：每个 M 在运行时绑定到一个 P，只有获得 P 的 M 才能执行 G。当一个 M 由于系统调用或阻塞操作（例如 IO）挂起时，调度器可能会将该 M 解除绑定，让其他 M 来接管 P 和 G 的执行。
5. **阻塞与唤醒**：当 M 执行的 Goroutine 被阻塞（如等待 IO），运行时会把该 M 挂起并让其他 M 接管执行。M 完成阻塞操作后会被唤醒并恢复执行。

### 3、GMP 模型的并发特点

- **并行执行**：由于多个 M 绑定不同的 P，同时执行不同的 G，GMP 模型可以充分利用多核 CPU，达到并行执行的效果。
- **工作窃取**：当某个 P 上的任务执行完毕，调度器会从其他 P 或全局队列中窃取任务，以保持各个处理器的负载均衡。
- **轻量级**：Goroutine 是非常轻量级的，相比于传统的操作系统线程，Goroutine 占用更少的内存和资源，调度的代价也更低。

### 4、总结

Golang 的 GMP 模型是通过将 Goroutine (G)、操作系统线程 (M) 和调度上下文 (P) 分离，来实现高效的并发调度机制。通过这种设计，Go 运行时可以在多核系统上高效地并行执行多个 Goroutine，减少阻塞和等待的影响，实现高效并发编程。

这种模型确保了 Go 程序的并发性能，并通过简化并发编程模型，让开发者能够更容易编写并发代码，而无需关心底层线程的管理。





## 六、``Golang`` 死锁

在 Go 语言中，死锁（deadlock）是一种并发问题，通常在两个或多个 ``goroutine`` 互相等待对方释放资源时发生，导致程序永久卡住且无法继续执行。Go 的运行时环境具有内置的死锁检测机制，当它检测到死锁时，会抛出运行时错误，并停止程序。

### 1、产生死锁的常见场景

1. **Channel 的错误使用**

   - 如果一个 ``goroutine`` 等待从未发送数据的 channel 中接收数据，而另一个 ``goroutine ``也在等待这个 channel 的操作，这会导致死锁。

   示例：

   ```go
   package main
   
   func main() {
       ch := make(chan int)
   
       // 主 goroutine 在等待从 ch 接收数据，但没有其他 goroutine 发送数据，导致死锁
       <-ch
   }
   ```

2. **互相等待锁**

   - 当多个 goroutine 需要获取相同的锁时，如果它们之间相互等待对方释放锁，也会导致死锁。

   示例：

   ```go
   package main
   
   import (
       "sync"
   )
   
   func main() {
       var mu1, mu2 sync.Mutex
   
       go func() {
           mu1.Lock()
           defer mu1.Unlock()
   
           mu2.Lock()
           defer mu2.Unlock()
       }()
   
       mu2.Lock()
       defer mu2.Unlock()
   
       mu1.Lock()
       defer mu1.Unlock()
   }
   ```

   在这个例子中，两个 goroutine 分别持有 `mu1` 和 `mu2`，并在等待对方释放锁，形成死锁。

### 2、如何避免死锁

1. **避免循环等待**：确保 goroutine 不会形成循环等待，可以通过规定锁的顺序来避免。例如，如果所有的 goroutine 都按相同顺序请求锁，那么可以避免互相等待。

2. **超时机制**：对于可能会发生阻塞的操作，可以引入超时机制来避免永久阻塞。例如，在 channel 上使用 `select` 加 `time.After` 来设定超时。

   示例：

   ```go
   package main
   
   import (
       "fmt"
       "time"
   )
   
   func main() {
       ch := make(chan int)
   
       select {
       case msg := <-ch:
           fmt.Println(msg)
       case <-time.After(2 * time.Second):
           fmt.Println("timeout")
       }
   }
   ```

3. **使用带缓冲的 Channel**：带缓冲的 channel 可以容纳一定数量的元素，避免 goroutine 被阻塞在发送或接收操作上。

4. **检查 Go 的 `race` 工具**：Go 提供了内置的 `race` 检测工具，可以帮助发现并发问题，包括死锁。

   使用方式：

   ```bash
   go run -race your_program.go
   ```

死锁是并发编程中常见的问题，了解其成因并采取适当措施，可以有效避免死锁的发生





## 七、``Golang ``协程泄露

在 ``Golang`` 中，协程泄露（``goroutine`` leak）是指协程启动后由于某种原因未正确结束，导致其持续存在并消耗系统资源。这种问题通常会导致内存消耗逐渐增加，系统性能下降，甚至出现资源耗尽的情况。

### 1、常见的协程泄露场景

1、**死锁**： 

当一个协程在等待某个事件（如通道消息）时，这个事件永远不会发生，导致协程一直阻塞在某个地方，无法退出。

```go
func deadlockExample() {
    ch := make(chan int)
    go func() {
        ch <- 1 // 无法接收消息，导致协程阻塞
    }()
}
```

2、**通道未关闭**：

 协程在等待从通道接收数据，但通道永远不会关闭，导致协程一直阻塞在接收操作上。

```go
func waitOnChannel() {
    ch := make(chan int)
    go func() {
        for v := range ch { // 如果通道没有关闭，协程将一直阻塞在这里
            fmt.Println(v)
        }
    }()
}
```

3、**竞争条件**： 

在某些情况下，协程可能依赖于某些共享资源或状态，如果这些资源没有及时释放或处理不当，协程也可能泄露。

```go
func raceConditionExample() {
    ch := make(chan int)
    go func() {
        select {
        case <-ch:
            // 等待通道消息，但通道可能永远不会发送消息
        case <-time.After(time.Second * 10):
            // 超时退出
        }
    }()
}
```

4、**不合理的循环**： 

如果协程中包含了无限循环或者过多的协程启动而没有合适的退出条件，也会导致协程泄露。

```go
func improperLoop() {
    go func() {
        for {
            // 永远不会退出的循环，协程无法结束
        }
    }()
}
```

### 2、如何防止协程泄露

1、**使用通道退出信号**： 

确保协程可以接收到退出信号。可以通过关闭通道或者使用 `context.Context` 控制协程退出。

```go
func worker(ctx context.Context, ch <-chan int) {
    for {
        select {
        case <-ctx.Done():
            return // 接收到退出信号，终止协程
        case v := <-ch:
            fmt.Println(v)
        }
    }
}

func main() {
    ch := make(chan int)
    ctx, cancel := context.WithCancel(context.Background())
    go worker(ctx, ch)

    // 模拟一段时间后取消任务
    time.Sleep(time.Second * 3)
    cancel()
}
```

2、**使用 `defer` 关闭通道**：

 在协程使用的通道中，及时关闭通道可以防止协程一直阻塞等待。

```go
func sendToChannel(ch chan int) {
    defer close(ch) // 确保通道被关闭
    ch <- 1
}
```

3、**避免不必要的协程**：

 尽量减少不必要的协程启动，只有在确实需要并且可以控制生命周期的情况下启动协程。

4、**使用 `sync.WaitGroup` 管理协程**：

 使用 `sync.WaitGroup` 来确保所有协程在主函数退出前都能正确完成。

```go
func worker(wg *sync.WaitGroup, id int) {
    defer wg.Done() // 协程结束时调用 Done
    fmt.Printf("Worker %d starting\n", id)
    time.Sleep(time.Second)
    fmt.Printf("Worker %d done\n", id)
}

func main() {
    var wg sync.WaitGroup
    for i := 1; i <= 5; i++ {
        wg.Add(1)
        go worker(&wg, i)
    }
    wg.Wait() // 等待所有协程结束
}
```

5、**监控协程数量**：

 可以通过 `runtime.NumGoroutine()` 函数监控当前运行的协程数量，帮助发现协程泄露问题。

```go
func monitorGoroutines() {
    fmt.Printf("Number of Goroutines: %d\n", runtime.NumGoroutine())
}
```

### 总结

协程泄露是 Golang 编程中常见的隐患之一，可能会导致系统资源消耗过多和程序不稳定。通过使用适当的控制机制（如 `context.Context`、通道关闭、`sync.WaitGroup` 等），可以有效避免协程泄露问题。此外，定期监控协程数量也是一个发现潜在问题的好方法。





## 八、channel 与锁

在 Go 语言的并发编程中，`channel` 和 `锁（sync.Mutex）` 是两种常见的并发控制工具，选择使用哪一种取决于你的具体需求和代码设计。以下是对 `channel` 和 `锁` 的比较以及选择依据。

### 1、Channel

`channel` 是 Go 语言中的通信机制，用于 goroutine 之间传递数据，主要体现的是 **通过通信来共享内存** 的思想。



**分类**

* 有缓冲
* 无缓冲



**适用场景**

1. **数据传递**：

   - 当你需要在 goroutine 之间传递数据，而不是单纯的共享内存时，`channel` 是非常适合的。它确保了数据的同步性和安全性。
   - 例如，生产者-消费者模式中，生产者将数据发送到 `channel`，消费者从 `channel` 中接收数据。

   示例：

   ```go
   package main
   
   import "fmt"
   
   func main() {
       ch := make(chan int)
   
       go func() {
           ch <- 42
       }()
   
       msg := <-ch
       fmt.Println(msg)
   }
   ```

2. **goroutine 协调**：

   - 如果你需要协调多个 goroutine 之间的工作，比如等待某个 goroutine 完成任务，`channel` 可以用于同步。

3. **广播数据**：

   - 可以将同一条数据通过多个 channel 进行广播给多个 goroutine。

**优势**

- **更高层次的抽象**：`channel` 将数据传递和同步操作结合在一起，使得代码更加简洁和清晰。
- **避免数据竞争**：通过将数据传递而不是共享内存，减少了数据竞争的可能性。
- **自动化同步**：`channel` 可以自动处理 goroutine 的同步和阻塞，避免手动加锁和解锁。

**缺点**

- **复杂性增加**：如果数据传递的逻辑复杂，`channel` 代码可能会变得难以理解和调试。
- **性能问题**：与直接访问共享内存相比，`channel` 的开销可能会更大，因为它包含了更多的同步机制。



### 2、锁（sync）

**分类**

* `sync.Mutex` 是一种互斥锁，用于保护共享的内存数据，确保只有一个 goroutine 可以访问某个临界区。





**死锁**





**适用场景**

1. **共享内存**：

   - 当多个 goroutine 需要同时访问或修改共享的内存数据时，锁是合适的工具。使用 `Mutex` 可以防止数据竞争问题。

   示例：

   ```go
   package main
   
   import (
       "fmt"
       "sync"
   )
   
   func main() {
       var mu sync.Mutex
       counter := 0
   
       go func() {
           mu.Lock()
           counter++
           mu.Unlock()
       }()
   
       mu.Lock()
       fmt.Println(counter)
       mu.Unlock()
   }
   ```

2. **简单的同步**：

   - 如果你的并发需求只是保护一个共享变量或临界区，不涉及 goroutine 之间的数据传递，`sync.Mutex` 是最简单直接的选择。

**优势**

- **性能较高**：锁的开销较低，特别是在需要频繁访问共享数据时，使用锁的性能通常比 `channel` 更好。
- **简洁直接**：锁的使用比较直观，特别是在保护简单的临界区时。

**缺点**

- **容易出错**：需要小心管理锁的获取和释放，特别是在复杂的逻辑中，容易出现死锁或忘记解锁的情况。
- **不能进行数据传递**：锁只能保护共享数据，并不能像 `channel` 一样实现数据的传递和通信。



### 3、选择依据

1. **数据流动还是数据保护**：
   - **数据流动**：如果你的并发逻辑主要是 goroutine 之间传递数据，那么 `channel` 更合适。
   - **数据保护**：如果你只是需要保护共享的内存不被多个 goroutine 同时访问，那么 `sync.Mutex` 更合适。
2. **代码复杂性**：
   - 如果你需要更高层次的抽象，减少手动的同步操作，`channel` 提供的通信模型可能更适合。
   - 如果你想避免复杂的并发逻辑，且只需保护少量的共享数据，锁是更直接的选择。
3. **性能需求**：
   - 对性能要求非常高且涉及大量并发的场景下，锁的性能通常优于 `channel`。
   - 但是如果需要处理更复杂的同步场景，比如多个 goroutine 之间的协调，`channel` 提供的灵活性和安全性可能更有利。



### 4、总结

- **使用 Channel**：当你的任务主要涉及 **goroutine 之间的通信** 或数据传递时，`channel` 是最佳选择，尤其是复杂的 goroutine 协作场景。
- **使用 Mutex**：当你需要保护 **共享内存** 或访问临界区时，`sync.Mutex` 更简单和高效，适合简单的并发控制。

根据任务需求选择合适的工具，将有助于实现更简洁、高效和可维护的并发程序。