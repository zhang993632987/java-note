# java虚拟机

## 1. JVM的运行机制

JVM（Java Virtual Machine）是用于运行Java字节码的虚拟机，包括一个**类加载器子系统（Class Loader SubSystem）**、**运行时数据区（Runtime Data Area）**、**执行引擎**和**本地接口库（Native Interface Library）**。

其中：

* **类加载器子系统**用于将编译好的Class文件加载到JVM中；
* **运行时数据区**用于存储在JVM运行过程中产生的数据，包括**程序计数器**、**方法区**、**本地方法区**、**虚拟机栈**和**虚拟机堆**；
* **执行引擎**包括**即时编译器**和**垃圾回收器**，即时编译器用于将Java字节码编译成具体的机器码，垃圾回收器用于回收在运行过程中不再使用的对象；

## 2. JVM的内存区域

JVM的内存区域分为**线程私有区域**（**程序计数器、虚拟机栈、本地方法栈**）、**线程共享区域**（**堆、方法区**）和**直接内存**。

**线程私有区域**的生命周期与线程相同，**随线程的启动而创建，随线程的结束而销毁**。

**线程共享区域随虚拟机的启动而创建，随虚拟机的关闭而销毁。**

### **1）程序计数器**

程序计数器是一块较小的内存空间，它可以看作是**当前线程所执行的字节码的行号指示器**。每个运行中的线程都有一个独立的程序计数器，在方法正在执行时，该方法的程序计数器记录的是实时虚拟机字节码指令的地址；如果该方法执行的是Native方法，则程序计数器的值为空（Undefined）。

程序计数器属于“线程私有”的内存区域，它是唯一没有规定OutOfMemoryError情况的区域。

### **2）虚拟机栈**

虚拟机栈描述的是Java方法执行的内存模型：每个方法在执行的同时都会创建一个**栈帧（Stack Frame)用于存储局部变量表**、**操作数栈**、**动态链接**、**方法出口**等信息。每一个方法从调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。

在Java虚拟机规范中，对这个区域规定了两种异常状况：如果线程请求的栈深度大于虚拟机所允许的深度，将抛出**StackOverflowError**异常；如果虚拟机栈可以动态扩展（当前大部分的Java虚拟机都可动态扩展，只不过Java虚拟机规范中也允许固定长度的虚拟机栈），如果扩展时无法申请到足够的内存，就会抛出**OutOfMemoryError**异常。

### **3）本地方法栈**

本地方法栈（Native Method Stack）与虚拟机栈所发挥的作用是非常相似的，它们之间的区别不过是虚拟机栈为虚拟机执行Java方法（也就是字节码）服务，而本地方法栈则为虚拟机使用到的Native方法服务。

与虚拟机栈一样，本地方法栈区域也会抛出**StackOverflowError**和**OutOfMemoryError**异常。

### **4）Java堆**

Java堆是被所有线程共享的一块内存区域，**在虚拟机启动时创建**。此内存区域的唯一目的就是**存放对象实例**，几乎所有的对象实例都在这里分配内存。

Java堆是垃圾收集器管理的主要区域，因此很多时候也被称做“GC堆”（Garbage Collected Heap）。

如果从分配内存的角度看，所有线程共享的Java堆中可以划分出多个线程私有的分配缓冲区（Thread Local Allocation Buffer， TLAB），以提升对象分配时的效率。

如果在堆中没有内存完成实例分配，并且堆也无法再扩展时，将会抛出**OutOfMemoryError**异常。

### **5）方法区**

方法区（Method Area）与Java堆一样，是各个线程共享的内存区域，它用于存储**已被虚拟机加载的类信息**、**常量**、**静态变量**、**即时编译器编译后的代码**等数据。

根据Java虚拟机规范的规定，当方法区无法满足内存分配需求时，将抛出OutOfMemoryError异常。

> _**运行时常量池**_
>
> 运行时常量池（Runtime Constant Pool）是方法区的一部分。Class文件中除了有类的版本、字段、方法、接口等描述信息外，还有一项信息是常量池（Constant Pool Table），用于存放编译期生成的各种字面量和符号引用，这部分内容将在类加载后进入方法区的运行时常量池中存放。
>
> 运行时常量池相对于Class文件常量池的一个重要特征是具备动态性，Java语言并不要求常量一定只有编译期才能产生，也就是并非预置入Class文件中常量池的内容才能进入方法区运行时常量池，运行期间也可能将新的常量放入池中，这种特性被开发人员利用得比较多的便是String类的intern()方法。
>
> 既然运行时常量池是方法区的一部分，自然受到方法区内存的限制，当常量池无法再申请到内存时会抛出**OutOfMemoryError**异常。

### **6）直接内存**

直接内存（Direct Memory）并不是虚拟机运行时数据区的一部分，也不是Java虚拟机规范中定义的内存区域。

在JDK 1.4中新加入了NIO（New Input/Output）类，引入了一种基于通道（Channel）与缓冲区（Buffer）的I/O方式，它可以使用Native函数库直接分配堆外内存，然后通过一个存储在Java堆中的DirectByteBuffer对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了在Java堆和Native堆中来回复制数据。

显然，本机直接内存的分配不会受到Java堆大小的限制，但是，既然是内存，肯定还是会受到本机总内存（包括RAM以及SWAP区或者分页文件）大小以及处理器寻址空间的限制。

## 3. 永久代与元空间

在Java 8中，永久代被元数据区（也叫作元空间）取代。元数据区的作用和永久代类似，二者最大的区别在于：**元数据区并没有使用虚拟机的内存**，而是**直接使用操作系统的本地内存**。因此，元空间的大小不受JVM内存的限制，只和操作系统的内存有关。

在Java 8中，JVM将类的元数据放入本地内存（Native Memory）中，将**常量池和类的静态变量放入Java堆**中，这样JVM能够加载多少元数据信息就不再由JVM的最大可用内存（MaxPermSize）空间决定，而由操作系统的实际可用内存空间决定。

```java
public class StringStore {
  public static void main(String[] args) {
    String str1 = new StringBuilder("计算机").append("软件").toString();
    System.out.println(str1.intern() == str1);
    
    String str2 = new StringBuilder("ja").append("va").toString();
    System.out.println(str2.intern() == str2);
  }
}
```

这段代码在JDK 1.6中运行，会得到两个false，而在JDK 1.7中运行，会得到一个true和一个false。

产生差异的原因是：

* **在JDK 1.6中，intern()方法会把首次遇到的字符串实例复制到永久代中，返回的也是永久代中这个字符串实例的引用**，而由StringBuilder创建的字符串实例在Java堆上，所以必然不是同一个引用，将返回false。
* **JDK 1.7的intern()实现不会再复制实例，只是在常量池中记录首次出现的实例引用，因此intern()返回的引用和由StringBuilder创建的那个字符串实例是同一个**。对str2比较返回false是因为“java”这个字符串在执行StringBuilder.toString()之前已经出现过，字符串常量池中已经有它的引用了，不符合“首次出现”的原则，而“计算机软件”这个字符串则是首次出现的，因此返回true。

## 4. Java中的4种引用类型

在JDK 1.2之后，Java对引用的概念进行了扩充，将引用分为**强引用（Strong Reference）**、**软引用（Soft Reference）**、**弱引用（Weak Reference）**、\*\*虚引用（Phantom Reference）\*\*4种，这4种引用强度依次逐渐减弱。

* 强引用就是指在程序代码之中普遍存在的，类似“Object obj = new Object()”这类的引用，**只要强引用还存在，垃圾收集器永远不会回收掉被引用的对象**。
* 软引用是用来描述一些还有用但并非必需的对象。**只被软引用关联着的对象，在系统将要发生内存溢出异常之前，将会把这些对象列进回收范围之中进行第二次回收。如果这次回收还没有足够的内存，才会抛出内存溢出异常**。在JDK 1.2之后，提供了SoftReference类来实现软引用。
* 弱引用也是用来描述非必需对象的，但是它的强度比软引用更弱一些，**只被弱引用关联的对象只能生存到下一次垃圾收集发生之前。当垃圾收集器工作时，无论当前内存是否足够，都会回收掉只被弱引用关联的对象**。在JDK 1.2之后，提供了WeakReference类来实现弱引用。
* 虚引用也称为幽灵引用或者幻影引用，它是最弱的一种引用关系。**一个对象是否有虚引用的存在，完全不会对其生存时间构成影响，也无法通过虚引用来取得一个对象实例。为一个对象设置虚引用关联的唯一目的就是能在这个对象被收集器回收时收到一个系统通知**。在JDK 1.2之后，提供了PhantomReference类来实现虚引用。

## 5. 如何确定垃圾

### **1）引用计数法**

在对象中添加一个引用计数器，每当有一个地方引用它时，计数器值就加一；当引用失效时，计数器值就减一；任何时刻计数器为零的对象就是不可能再被使用的。

引用计数法无法解决循环引用问题。

### **2）可达性分析**

可达性分析算法的基本思路是通过一些列称为“GC Roots”的根对象作为起始节点集，从这些节点开始，根据引用关系向下搜索，搜索过程所走过的路径称为“引用链（Reference Chain）”，如果某个对象到GC Roots间没有任何引用链相连，或者用图论的话来说就是从GC Roots到这个对象不可达时，则证明此对象是不可能再被使用的。

## 6. 垃圾回收算法

### **1）标记清除算法**

标记清除算法是基础的垃圾回收算法，其过程分为**标记**和**清除**两个阶段。在标记阶段标记所有需要回收的对象，在清除阶段清除可回收的对象并释放其所占用的内存空间。

它的主要**不足**有两个：一个是**效率问题**，标记和清除两个过程的效率都不高；另一个是**空间问题**，标记清除之后会产生大量不连续的内存碎片，**空间碎片太多**可能会导致以后在程序运行过程中需要分配较大对象时，无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。

### **2）标记复制算法**

复制算法将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用过的内存空间一次清理掉。这样使得每次都是对整个半区进行内存回收，内存分配时也就不用考虑内存碎片等复杂情况，只要移动堆顶指针，按顺序分配内存即可，实现简单，运行高效。只是这种算法的代价是将内存缩小为了原来的一半，未免太高了一点。

### **3）标记整理算法**

标记整理算法结合了标记清除算法和复制算法的优点，其标记阶段和标记清除算法的标记阶段相同，在标记完成后，让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存。

标记清除算法与标记整理算法的本质差异在于前者是一种非移动式的回收算法，而后者是移动式的。移动对象会使得内存回收时更复杂，不移动则内存分配时会更复杂；从垃圾收集的停顿时间来看，不移动对象停顿时间会更短，甚至可以不需要停顿，但是从整个程序的吞吐量来看，移动对象会更划算。即使不移动对象会使得收集器的效率提升一些，但因内存分配和访问相比垃圾收集的频率要高得多，内存分配的耗时增加，因此最终总吞吐量是下降的。

Hotspot虚拟机里面关注吞吐量的Parallel Scavenge收集器是基于标记整理算法的，而关注延迟的CMS收集器则是基于标记清除算法的。

### **4）分代收集算法**

分代收集算法根据对象的不同类型将内存划分为不同的区域，JVM将堆划分为**新生代**和**老年代**。新生代主要存放新生成的对象，其特点是对象数量多但是生命周期短，在每次进行垃圾回收时都有大量的对象被回收；老年代主要存放大对象和生命周期长的对象，因此可回收的对象相对较少。因此，JVM根据不同的区域对象的特点选择了不同的算法。

目前，大部分JVM在**新生代都采用了复制算法**，因为在新生代中每次进行垃圾回收时都有大量的对象被回收，需要复制的对象（存活的对象）较少，不存在大量的对象在内存中被来回复制的问题，因此采用复制算法能安全、高效地回收新生代大量的短生命周期的对象并释放内存。

老年代主要存放生命周期较长的对象和大对象，因而每次只有少量非存活的对象被回收，因而在**老年代采用标记清除或标记整理算法**。

JVM内存中的对象主要被分配到新生代的Eden区和ServivorFrom区，在少数情况下会被直接分配到老年代。在新生代的Eden区和ServivorFrom区的内存空间不足时会触发一次GC，该过程被称为MinorGC。在MinorGC后，在Eden区和ServivorFrom区中存活的对象会被复制到ServivorTo区，然后Eden区和ServivorFrom区被清理。如果此时在ServivorTo区无法找到连续的内存空间存储某个对象，则将这个对象直接存储到老年代。若Servivor区的对象经过一次GC后仍然存活，则其年龄加1。在默认情况下，对象在年龄达到15时，将被移到老年代。
