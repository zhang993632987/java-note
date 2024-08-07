# Java 内存模型

处理器要与内存交互，如读取运算数据、存储运算结果等，这个 I/O 操作就是很难消除的（无法仅靠寄存器来完成所有运算任务）。**由于计算机的存储设备与处理器的运算速度有着几个数量级的差距，所以现代计算机系统都不得不加入一层或多层读写速度尽可能接近处理器运算速度的**<mark style="color:blue;">**高速缓存（Cache）**</mark>**来作为内存与处理器之间的缓冲**：将运算需要使用的数据复制到缓存中，让运算能快速进行，当运算结束后再从缓存同步回内存之中，这样处理器就无须等待缓慢的内存读写了。

基于高速缓存的存储交互很好地解决了处理器与内存速度之间的矛盾，但是也为计算机系统带来更高的复杂度，它引入了一个新的问题：<mark style="color:blue;">**缓存一致性（Cache Coherence）**</mark>。**当多个处理器的运算任务都涉及同一块主内存区域时，将可能导致各自的缓存数据不一致。**

为了解决一致性的问题，需要各个处理器访问缓存时都遵循一些协议，在读写时要根据协议来进行操作，这类协议有 MSI、MESI（Illinois Protocol）、MOSI、Synapse、Firefly 及 Dragon Protocol 等。

{% hint style="success" %}
**内存模型可以理解为在特定的操作协议下，对特定的内存或高速缓存进行读写访问的过程抽象。**

不同架构的物理机器可以拥有不一样的内存模型。
{% endhint %}

{% hint style="success" %}
**Java 内存模型屏蔽了各种硬件和操作系统的内存访问差异，以实现让 Java 程序在各种平台下都能达到一致的内存访问效果。**
{% endhint %}
