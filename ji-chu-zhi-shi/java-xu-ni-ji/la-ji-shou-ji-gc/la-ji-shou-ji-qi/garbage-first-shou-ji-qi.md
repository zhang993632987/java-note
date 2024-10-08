# Garbage First 收集器

G1 是一款主要**面向服务端应用**的垃圾收集器。

与 CMS 的“标记-清除”算法不同，<mark style="color:blue;">**G1 从整体来看是基于“标记-整理”算法实现的收集器**</mark>，但<mark style="color:blue;">**从局部（两个 Region 之间）上看又是基于“标记-复制”算法**</mark>实现**。这两种算法都意味着 G1 运作期间不会产生内存空间碎片，垃圾收集完成之后能提供规整的可用内存。这种特性有利于程序长时间运行，在程序为大对象分配内存时不容易因无法找到连续内存空间而提前触发下一次收集。**

## Region

在 G1 收集器出现之前的所有其他收集器，包括 CMS 在内，垃圾收集的目标范围要么是整个新生代（Minor GC），要么就是整个老年代（Major GC），再要么就是整个 Java 堆（Full GC）。而 <mark style="color:blue;">**G1 可以面向堆内存任何部分来组成回收集（Collection Set，一般简称 CSet）进行回收，衡量标准不再是它属于哪个分代，而是哪块内存中存放的垃圾数量最多，回收收益最大**</mark>，这就是 G1 收集器的 **Mixed GC 模式**。

<mark style="color:blue;">**G1 开创的基于 Region 的堆内存布局是它能够实现这个目标的关键**</mark>**。**虽然 G1 也仍是遵循分代收集理论设计的，但其堆内存的布局与其他收集器有非常明显的差异：<mark style="color:blue;">**G1 不再坚持固定大小以及固定数量的分代区域划分，而是把连续的 Java 堆划分为多个大小相等的独立区域（Region），每一个 Region 都可以根据需要，扮演新生代的 Eden 空间、Survivor 空间，或者老年代空间。**</mark>收集器能够对扮演不同角色的 Region 采用不同的策略去处理，这样无论是新创建的对象还是已经存活了一段时间、熬过多次收集的旧对象都能获得很好的收集效果。

<mark style="color:blue;">**虽然 G1 仍然保留新生代和老年代的概念，但新生代和老年代不再是固定的了，它们都是一系列区域（不需要连续）的动态集合。**</mark>G1 收集器之所以能建立可预测的停顿时间模型，是因为它<mark style="color:blue;">**将 Region 作为单次回收的最小单元，即每次收集到的内存空间都是 Region 大小的整数倍**</mark>，这样可以有计划地避免在整个 Java 堆中进行全区域的垃圾收集。更具体的处理思路是**让 G1 收集器去跟踪各个 Region 里面的垃圾堆积的“价值”大小，价值即回收所获得的空间大小以及回收所需时间的经验值，然后在后台维护一个优先级列表，每次根据用户设定允许的收集停顿时间（使用参数 -XX:MaxGCPauseMillis 指定，默认值是 200 毫秒），优先处理回收价值收益最大的那些 Region。**这种使用 Region 划分内存空间，以及具有优先级的区域回收方式，保证了 G1 收集器在有限的时间内获取尽可能高的收集效率。

{% hint style="success" %}
**Humongous 区域**

Region 中还有一类特殊的 **Humongous 区域**，专门用来存储大对象。

G1 认为只要大小超过了一个 Region 容量一半的对象即可判定为大对象。每个 Region 的大小可以通过参数 -XX:G1HeapRegionSize 设定，取值范围为 1MB～32MB，且应为 2 的 N 次幂。

对于那些超过了整个 Region 容量的超级大对象，将会被存放在 N 个连续的 Humongous Region 之中，G1 的大多数行为都把 Humongous Region 作为老年代的一部分来进行看待。
{% endhint %}

## 运行过程

G1 收集器的运作过程大致可划分为以下四个步骤：

1. **初始标记（Initial Marking）：**仅仅只是标记一下 GC Roots 能直接关联到的对象。**这个阶段需要停顿线程，但耗时很短，而且是借用进行 Minor GC 的时候同步完成的，所以 G1 收集器在这个阶段实际并没有额外的停顿。**
2. **并发标记（Concurrent Marking）：**从 GC Roots 开始对堆中对象进行可达性分析，递归扫描整个堆里的对象图，找出要回收的对象，这阶段耗时较长，但**可与用户程序并发执行**。
3. **最终标记（Final Marking）：对用户线程做另一个短暂的暂停**，用于处理并发阶段结束后仍遗留下来的最后那少量的 SATB 记录。
4. **筛选回收（Live Data Counting and Evacuation）：**负责更新 Region 的统计数据，<mark style="color:blue;">**对各个 Region 的回收价值和成本进行排序，根据用户所期望的停顿时间来制定回收计划**</mark>，可以自由选择任意多个 Region 构成回收集，然后把决定回收的那一部分 Region 的存活对象复制到空的 Region 中，再清理掉整个旧 Region 的全部空间。**这里的操作涉及存活对象的移动，是必须暂停用户线程，由多条收集器线程并行完成的。**

<mark style="color:blue;">**G1 收集器除了并发标记外，其余阶段也是要完全暂停用户线程的**</mark>**。**它**并非纯粹地追求低延迟**，官方给它设定的目标是<mark style="color:blue;">**在延迟可控的情况下获得尽可能高的吞吐量。**</mark>

{% hint style="success" %}
从 G1 开始，最先进的垃圾收集器的设计导向都不约而同地变为追求**能够应付应用的内存分配速率（Allocation Rate），而不追求一次把整个 Java 堆全部清理干净。**这样，**应用在分配，同时收集器在收集，只要收集的速度能跟得上对象分配的速度，那一切就能运作得很完美。**
{% endhint %}
