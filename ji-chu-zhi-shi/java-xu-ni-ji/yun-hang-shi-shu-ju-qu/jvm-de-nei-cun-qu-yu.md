# JVM 的内存区域

运行时JVM 的内存区域分为<mark style="color:blue;">**线程私有区域**</mark><mark style="color:blue;">（</mark><mark style="color:blue;">**程序计数器、虚拟机栈、本地方法栈**</mark><mark style="color:blue;">）</mark>、<mark style="color:blue;">**线程共享区域**</mark><mark style="color:blue;">（</mark><mark style="color:blue;">**堆、方法区**</mark><mark style="color:blue;">）</mark>和<mark style="color:blue;">**直接内存**</mark>。

* **线程私有区域**的生命周期与线程相同，**随线程的启动而创建，随线程的结束而销毁**。
* **线程共享区域随虚拟机的启动而创建，随虚拟机的关闭而销毁。**

## **程序计数器**

程序计数器是一块较小的内存空间，它可以看作是<mark style="color:blue;">**当前线程所执行的字节码的行号指示器**</mark>。

* 每个运行中的线程都有一个独立的程序计数器，在方法正在执行时，该方法的程序计数器记录的是实时虚拟机字节码指令的地址；
* 如果该方法执行的是 Native 方法，则程序计数器的值为空（Undefined）。

**程序计数器属于“线程私有”的内存区域，它是唯一没有规定 OutOfMemoryError 情况的区域。**

## **虚拟机栈**

虚拟机栈描述的是 Java 方法执行的内存模型：**每个方法在执行的同时都会创建一个**<mark style="color:blue;">**栈帧（Stack Frame)**</mark>**用于存储局部变量表、操作数栈、动态链接、方法出口等信息。**<mark style="color:blue;">**每一个方法从调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。**</mark>

在 Java 虚拟机规范中，对这个区域规定了两种异常状况：

* **如果线程请求的栈深度大于虚拟机所允许的深度**，将抛出 **StackOverflowError** 异常；
* 如果虚拟机栈可以动态扩展（当前大部分的 Java 虚拟机都可动态扩展，只不过 Java 虚拟机规范中也允许固定长度的虚拟机栈），**如果扩展时无法申请到足够的内存**，就会抛出 **OutOfMemoryError** 异常。

## **本地方法栈**

本地方法栈（Native Method Stack）与虚拟机栈所发挥的作用是非常相似的，它们之间的区别不过是虚拟机栈为虚拟机执行 Java 方法（也就是字节码）服务，而本地方法栈则为虚拟机使用到的 Native 方法服务。

与虚拟机栈一样，本地方法栈区域也会抛出 **StackOverflowError** 和 **OutOfMemoryError** 异常。

## **Java 堆**

Java 堆是被所有线程共享的一块内存区域，**在虚拟机启动时创建**。此内存区域的唯一目的就是**存放对象实例**，几乎所有的对象实例都在这里分配内存。

Java 堆是垃圾收集器管理的主要区域，因此很多时候也被称做**“GC 堆”（Garbage Collected Heap）**。

> 如果从分配内存的角度看，所有线程共享的 Java 堆中可以划分出多个线程私有的分配缓冲区（Thread Local Allocation Buffer，TLAB），以提升对象分配时的效率。

**如果在堆中没有内存完成实例分配，并且堆也无法再扩展时**，将会抛出 **OutOfMemoryError** 异常。

## **方法区**

方法区（Method Area）与 Java 堆一样，是各个线程共享的内存区域，它用于存储**已被虚拟机加载的类信息**、**常量**、**静态变量**、**即时编译器编译后的代码**等数据。

根据 Java 虚拟机规范的规定，**当方法区无法满足内存分配需求时**，将抛出 OutOfMemoryError 异常。

<details>

<summary><mark style="color:purple;"><strong>运行时常量池</strong></mark></summary>

**运行时常量池（Runtime Constant Pool）是方法区的一部分。**

Class 文件中除了有类的版本、字段、方法、接口等描述信息外，还有一项信息是常量池（Constant Pool Table），用于存放编译期生成的各种字面量和符号引用，这部分内容将在类加载后进入方法区的运行时常量池中存放。

**运行时常量池相对于 Class 文件常量池的一个重要特征是具备动态性，**Java 语言并不要求常量一定只有编译期才能产生，也就是并非预置入 Class 文件中常量池的内容才能进入方法区的运行时常量池，运行期间也可能将新的常量放入池中，这种特性被开发人员利用得比较多的便是 String 类的 intern() 方法。

既然运行时常量池是方法区的一部分，自然受到方法区内存的限制，当常量池无法再申请到内存时会抛出 **OutOfMemoryError** 异常。

</details>

### **直接内存**

直接内存（Direct Memory）并不是虚拟机运行时数据区的一部分，也不是 Java 虚拟机规范中定义的内存区域。

在 JDK 1.4 中新加入了 NIO（New Input/Output）类，引入了一种基于通道（Channel）与缓冲区（Buffer）的 I/O 方式，它可以使用 Native 函数库直接分配堆外内存，然后通过一个存储在 Java 堆中的 DirectByteBuffer 对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了在Java 堆和 Native 堆中来回复制数据。

显然，本机直接内存的分配不会受到 Java 堆大小的限制，但是，既然是内存，肯定还是会受到本机总内存（包括 RAM 以及 SWAP 区或者分页文件）大小以及处理器寻址空间的限制。
