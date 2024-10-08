# 垃圾回收算法

### <mark style="color:blue;">**标记清除算法**</mark>

标记清除算法是基础的垃圾回收算法，其过程分为**标记**和**清除**两个阶段。**在标记阶段标记所有需要回收的对象，在清除阶段清除可回收的对象并释放其所占用的内存空间。**

它的主要**不足**有两个：

* 一个是**效率问题**，标记和清除两个过程的效率都不高；
* 另一个是**空间问题**，标记清除之后会产生大量不连续的内存碎片，**空间碎片太多**可能会导致以后在程序运行过程中需要分配较大对象时，无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。

### <mark style="color:blue;">**标记复制算法**</mark>

复制算法将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就**将还存活着的对象复制到另外一块上面**，然后**再把已使用过的内存空间一次性清理掉**。这样使得每次都是对整个半区进行内存回收，内存分配时也就不用考虑内存碎片等复杂情况，只要移动堆顶指针，按顺序分配内存即可，实现简单，运行高效。

只是**这种算法的代价是将内存缩小为了原来的一半**，未免太高了一点。

### <mark style="color:blue;">**标记整理算法**</mark>

标记整理算法结合了标记清除算法和复制算法的优点，其标记阶段和标记清除算法的标记阶段相同，**在标记完成后，让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存。**

> **标记清除算法与标记整理算法的本质差异在于前者是一种非移动式的回收算法，而后者是移动式的。**
>
> * 移动对象会使得内存回收时更复杂，不移动则使得内存分配时会更复杂；
> * 从垃圾收集的停顿时间来看，不移动对象停顿时间会更短，甚至可以不需要停顿；
> * 但是从整个程序的吞吐量来看，移动对象会更划算。即使不移动对象会使得收集器的效率提升一些，但因内存分配和访问相比垃圾收集的频率要高得多，内存分配的耗时增加，因此最终总吞吐量是下降的。

<mark style="color:blue;">**Hotspot 虚拟机里面关注吞吐量的 Parallel Old 收集器是基于标记整理算法的，而关注延迟的 CMS 收集器则是基于标记清除算法的。**</mark>

### <mark style="color:blue;">**分代收集算法**</mark>

分代收集算法**根据对象的不同类型将内存划分为不同的区域**，JVM 将堆划分为**新生代**和**老年代**。

* **新生代主要存放新生成的对象，其特点是对象数量多但是生命周期短，在每次进行垃圾回收时都有大量的对象被回收；**
* **老年代主要存放大对象和生命周期长的对象，因此可回收的对象相对较少。**

因此，**JVM 根据不同的区域对象的特点选择了不同的算法。**

目前，大部分 JVM 在**新生代都采用了复制算法**，因为在新生代中每次进行垃圾回收时都有大量的对象被回收，需要复制的对象（存活的对象）较少，不存在大量的对象在内存中被来回复制的问题，因此采用复制算法能安全、高效地回收新生代大量的短生命周期的对象并释放内存。

老年代主要存放生命周期较长的对象和大对象，因而每次只有少量非存活的对象被回收，因而在**老年代采用标记清除或标记整理算法**。

* [x] JVM 内存中的对象主要被分配到新生代的 Eden 区和 ServivorFrom 区，在少数情况下会被直接分配到老年代。
* [x] 在新生代的 Eden 区和 ServivorFrom 区的内存空间不足时会触发一次 GC，该过程被称为 MinorGC。
* [x] 在 MinorGC 后，在 Eden 区和 ServivorFrom 区中存活的对象会被复制到 ServivorTo 区，然后 Eden 区和 ServivorFrom 区被清理。
* [x] 如果此时在 ServivorTo 区无法找到连续的内存空间存储某个对象，则将这个对象直接存储到老年代。
* [x] 若 Servivor 区的对象经过一次 GC 后仍然存活，则其年龄加 1。在默认情况下，对象在年龄达到 15 时，将被移到老年代。
