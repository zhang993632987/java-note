# Serial Old 收集器

**Serial Old 是 Serial 收集器的老年代版本**，它同样是一个**单线程收集器**，**使用标记-整理算法**。

**这个收集器的主要意义是供客户端模式下的 HotSpot 虚拟机使用。**

如果**在服务端模式下**，它也可能有两种用途：

* 一种是在 JDK 5 以及之前的版本中与 Parallel Scavenge 收集器搭配使用；
* 另外一种就是**作为 CMS 收集器发生失败时的后备预案，在并发收集发生 Concurrent Mode Failure 时使用**。
