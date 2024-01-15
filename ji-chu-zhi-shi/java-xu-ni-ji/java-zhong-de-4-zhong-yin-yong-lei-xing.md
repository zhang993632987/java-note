# Java 中的 4 种引用类型

在 JDK 1.2 之后，Java 对引用的概念进行了扩充，将引用分为<mark style="color:blue;">**强引用（Strong Reference）**</mark>、<mark style="color:blue;">**软引用（Soft Reference）**</mark>、<mark style="color:blue;">**弱引用（Weak Reference）**</mark>、<mark style="color:blue;">**虚引用（Phantom Reference）**</mark>4 种，这 4 种引用强度依次逐渐减弱。

* 强引用就是指在程序代码之中普遍存在的，类似“Object obj = new Object()”这类的引用，<mark style="color:blue;">**只要强引用还存在，垃圾收集器永远不会回收掉被引用的对象**</mark><mark style="color:blue;">。</mark>
* 软引用是用来描述一些还有用但并非必需的对象。<mark style="color:blue;">**只被软引用关联着的对象，在系统将要发生内存溢出异常之前，将会把这些对象列进回收范围之中进行第二次回收。如果这次回收还没有足够的内存，才会抛出内存溢出异常**</mark><mark style="color:blue;">。</mark>在 JDK 1.2 之后，提供了 **SoftReference** 类来实现软引用。
* 弱引用也是用来描述非必需对象的，但是它的强度比软引用更弱一些，<mark style="color:blue;">**只被弱引用关联的对象只能生存到下一次垃圾收集发生之前。当垃圾收集器工作时，无论当前内存是否足够，都会回收掉只被弱引用关联的对象**</mark>。在 JDK 1.2 之后，提供了 **WeakReference** 类来实现弱引用。
* 虚引用也称为幽灵引用或者幻影引用，它是最弱的一种引用关系。<mark style="color:blue;">**一个对象是否有虚引用的存在，完全不会对其生存时间构成影响，也无法通过虚引用来取得一个对象实例。**</mark>**为一个对象设置虚引用关联的唯一目的就是能在这个对象被收集器回收时收到一个系统通知**。在 JDK 1.2 之后，提供了**PhantomReference** 类来实现虚引用。
