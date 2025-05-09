# GC

## 一、G1

是一种在Java虚拟机（JVM）中实现的垃圾收集器，旨在提高具有大内存和多处理器/多核CPU的服务器应用程序的性能。G1GC（Garbage-First Garbage Collector）是这种垃圾收集器的全称，它自Java 7 Update 4起作为实验性功能被引入，并在Java 8中成为正式的生产级垃圾收集器



### G1GC的关键特性：

1. **分区（Region-Based Heap）**：
   G1将堆内存划分为多个大小相等的独立区域（Region），每个Region可以是年轻代（Eden区、Survivor区）或老年代的一部分。这种设计使得G1能够灵活地管理内存，根据应用程序的需求动态调整各代的大小。

2. **停顿时间控制**：
   G1GC允许用户设置期望的停顿时间目标（例如，通过`-XX:MaxGCPauseMillis`参数），G1会尽力在垃圾收集时接近这个目标，但并不能保证总是能达到。G1使用复杂的预测模型和启发式算法来平衡垃圾收集的频率和停顿时间。

3. **并发性和并行性**：
   G1GC支持并发收集，即垃圾收集可以在应用程序运行时进行，以减少对应用程序性能的影响。同时，G1也利用多核CPU的优势，在垃圾收集过程中并行执行多个任务，以提高收集效率。

4. **增量式整理（Incremental Compaction）**：
   为了减少内存碎片，G1在垃圾收集过程中会进行增量式整理。这意味着G1会逐步将存活的对象从多个区域中移动到连续的空间中，而不是在每次垃圾收集时都进行完整的整理。

5. **可预测性**：
   通过停顿时间控制和增量式整理，G1GC努力为应用程序提供可预测的垃圾收集性能。这对于需要稳定响应时间的应用程序尤为重要。

   



### G1GC的使用场景：

G1GC特别适用于以下场景：

- 需要低停顿时间的应用程序，如实时系统或高响应要求的Web服务器。
- 具有大内存（通常大于4GB）和多核CPU的服务器应用程序。
- 应用程序的内存分配和回收模式复杂，难以通过传统的垃圾收集器（如Parallel GC或CMS）进行优化。



### 如何启用G1GC：

在JVM启动时，可以通过设置`-XX:+UseG1GC`参数来启用G1垃圾收集器。此外，还可以根据需要设置其他与G1GC相关的JVM参数，如`-XX:MaxGCPauseMillis`（设置最大停顿时间目标）、`-Xms`和`-Xmx`（分别设置JVM的初始堆大小和最大堆大小）等。



### 总结：

G1GC是一种面向服务器应用的先进垃圾收集器，它通过分区、停顿时间控制、并发性和并行性等机制，为大型、多核服务器应用程序提供了高效、可预测的垃圾收集解决方案。随着Java应用的不断发展，G1GC已成为许多高性能应用的首选垃圾收集器。



## 二、G1优化策略

G1（Garbage-First）垃圾回收器是一种面向服务器应用的垃圾回收器，旨在在满足低延迟和高吞吐量的同时，尽量减少垃圾回收暂停时间。以下是针对G1优化的一些策略：

### 1. 堆大小设置

- **选择合适的堆大小**：根据应用的实际负载和内存资源，选择合适的堆大小。过小的堆会导致频繁的垃圾回收，而过大的堆会增加回收时间。
- **避免手动设置新生代和老年代大小**：G1收集器会自动调整新生代和老年代的大小，以优化暂停时间和吞吐量。手动设置这些值可能会干扰G1的自动调优机制。

### 2. 暂停时间目标

- **设置合理的暂停时间目标**：使用`-XX:MaxGCPauseMillis`参数设置垃圾回收的最大暂停时间。通常设置为100ms到200ms是合理的，但具体值应根据应用需求进行调整。
- **持续调优暂停时间**：由于不同情况下应用的内存使用模式可能会有所不同，因此需要对暂停时间目标进行持续调优，以找到最佳平衡点。

### 3. 并发线程设置

- **增加并发GC线程数量**：使用`-XX:ConcGCThreads=n`参数增加标记线程的数量，以加速垃圾回收过程。这有助于在高并发场景下减少暂停时间。

### 4. Mixed GC调优

- **调整混合垃圾回收的参数**：使用`-XX:InitiatingHeapOccupancyPercent`、`-XX:G1MixedGCLiveThresholdPercent`、`-XX:G1MixedGCCountTarget`等参数来调优混合垃圾回收的行为。这些参数可以控制何时开始混合垃圾回收、回收哪些区域以及回收多少区域等。

### 5. 监控与调优

- **使用监控工具**：使用GC日志、JVM监控工具等实时监测系统的垃圾回收情况。根据监控结果进行调优和优化，确保G1垃圾回收器的性能表现在理想状态。
- **定期检查代码**：高并发系统容易出现内存泄漏问题，导致垃圾回收器无法回收不再使用的对象。要定期检查代码，避免内存泄漏，减少垃圾回收的负担。

### 6. 其他优化策略

- **禁用透明大页面（THP）**：在Linux系统中，透明大页面功能可能会导致随机进程停止，从而影响G1垃圾回收器的性能。建议禁用此功能以提高稳定性。
- **为日志提供单独的磁盘**：写入日志可能会与后台任务占用I/O资源而阻塞，为日志或其他存储提供单独的磁盘可以避免这种情况。
- **优化应用程序**：在某些情况下，对应用程序进行优化可能比调整JVM参数更有效。例如，减少大对象的数量、优化数据结构等都可以降低垃圾回收的负担。

### 7. 预热机制

- **使用预热机制**：在高并发系统启动后，可以使用预热机制进行一段时间的运行，让G1垃圾回收器逐渐优化内存回收策略，减少后续的暂停时间。

综上所述，G1垃圾回收器的优化是一个持续的过程，需要根据应用的实际需求和监控结果进行调整。通过合理配置堆大小、启用并行处理线程、设置相关参数等手段，结合实时监控和调优，可以最大程度地优化G1垃圾回收器的性能，提升高并发系统的稳定性和吞吐量。