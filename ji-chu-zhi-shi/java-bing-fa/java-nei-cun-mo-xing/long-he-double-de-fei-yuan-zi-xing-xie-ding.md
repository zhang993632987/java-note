# long 和 double 的非原子性协定

<mark style="color:blue;">**Java 内存模型允许虚拟机将没有被 volatile 修饰的 64 位数据的读写操作划分为两次 32 位的操作来进行**</mark>，即允许虚拟机实现自行选择是否要保证 64 位数据类型的原子性，这就是所谓的“**long 和 double 的非原子性协定**”（Non-Atomic Treatment of double and long Variables）。

如果有多个线程共享一个并未声明为 volatile 的 long 或 double 类型的变量，并且同时对它们进行读取和修改操作，那么某些线程可能会读取到一个既不是原值，也不是其他线程修改值的代表了“半个变量”的数值。

不过这种读取到“半个变量”的情况是非常罕见的，经过实际测试<mark style="color:blue;">**(**</mark>[<mark style="color:blue;">**https://shipilev.net/blog/2014/all-accesses-are-atomic/**</mark>](https://shipilev.net/blog/2014/all-accesses-are-atomic/)<mark style="color:blue;">**)**</mark>：

* **在目前主流平台下商用的 64 位 Java 虚拟机中并不会出现非原子性访问行为；**
* 但是对于 32 位的 Java 虚拟机，譬如比较常用的 **32 位 x86 平台下的 HotSpot 虚拟机，对 long 类型的数据确实存在非原子性访问的风险**。
* 而**针对 double 类型**，由于现代中央处理器中一般都包含专门用于处理浮点数据的浮点运算器（Floating Point Unit，FPU），用来专门处理单、双精度的浮点数据，所以**哪怕是 32 位虚拟机中通常也不会出现非原子性访问的问题。**
