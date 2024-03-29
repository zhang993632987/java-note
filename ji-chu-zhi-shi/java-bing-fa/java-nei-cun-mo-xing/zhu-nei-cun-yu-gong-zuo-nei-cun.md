# 主内存与工作内存

**Java 内存模型的主要目的是定义程序中各种变量的访问规则**，即关注在虚拟机中把变量值存储到内存和从内存中取出变量值这样的底层细节。

> 此处的变量（Variables）与 Java 编程中所说的变量有所区别，它包括了实例字段、静态字段和构成数组对象的元素，但是不包括局部变量与方法参数[^1]，因为后者是线程私有的，不会被共享，自然就不会存在竞争问题。

Java 内存模型规定了**所有的变量都存储在**<mark style="color:blue;">**主内存（Main Memory）**</mark>**中** 。**每条线程都有自己的**<mark style="color:blue;">**工作内存（Working Memory ）**</mark>**，**[**线程的工作内存中保存了被该线程使用的变量的主内存副本**](#user-content-fn-2)[^2]**。**

* <mark style="color:orange;">**线程对变量的所有操作（读取、赋值等）都必须在工作内存中进行，而不能直接读写主内存中的数据。**</mark>
* <mark style="color:orange;">**不同的线程之间无法直接访问对方工作内存中的变量，线程间变量值的传递均需要通过主内存来完成。**</mark>

线程、主内存、工作内存三者的交互关系如图所示：

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

> **根据《Java 虚拟机规范》的约定，volatile 变量依然有工作内存的拷贝，但是由于它特殊的操作顺序性规定，所以看起来如同直接在主内存中读写访问一般。**



[^1]: **如果局部变量是一个 reference 类型，它引用的对象在 Java 堆中可被各个线程共享，但是 reference 本身在 Java 栈的局部变量表中是线程私有的。**

[^2]: **假设线程中访问一个 10MB 大小的对象，也会把这 10MB 的内存复制一份出来吗？**

    事实上**并不会如此**，**这个对象的引用、对象中某个在线程访问到的字段是有可能被复制的，但不会有虚拟机把整个对象复制一次。**
