# OutOfMemoryError

**除了程序计数器外，虚拟机内存的其他几个运行时区域都有发生 OutOfMemoryError（OOM）异常的可能。**

> **通过参数 **<mark style="color:blue;">**-XX:+HeapDumpOnOutOfMemoryError**</mark>** 可以让虚拟机在出现内存溢出异常的时候 Dump 出当前的内存堆转储快照以便进行事后分析。**

## Java 堆溢出

<details>

<summary><mark style="color:purple;">实验类</mark></summary>

```java
/**
 * VM Args：-Xms20m -Xmx20m -XX:+HeapDumpOnOutOfMemoryError
 *
 * @author Zhang B H
 * @create 2024-01-15 21:28
 */
public class HeapOOM {

    static class OOMObject {
    }

    public static void main(String[] args) {
        List<OOMObject> list = new ArrayList<>();
        while (true) {
            list.add(new OOMObject());
        }
    }
}
```

</details>

### 异常信息

```java
java.lang.OutOfMemoryError: Java heap space
Dumping heap to java_pid3404.hprof ...
Heap dump file created [22045981 bytes in 0.663 secs]
```

**出现 Java 堆内存溢出时，异常堆栈信息 “java.lang.OutOfMemoryError” 会跟随进一步的提示 “**<mark style="color:blue;">**Java heap space**</mark>**”。**

### **解决方案**

要解决这个内存区域的异常，常规的处理方法是**首先通过**<mark style="color:blue;">**内存映像分析工具**</mark>**（如 Eclipse Memory Analyzer）对 Dump 出来的堆转储快照进行分析。确认内存中导致 OOM 的对象是否是必要的，也就是要先分清楚到底是出现了内存泄漏（Memory Leak）还是内存溢出（Memory Overflow）。**

* **如果是内存泄漏，可进一步通过工具查看泄漏对象到 GC Roots 的引用链，找到泄漏对象是通过怎样的引用路径、与哪些 GC Roots 相关联，才导致垃圾收集器无法回收它们**，根据泄漏对象的类型信息以及它到 GC Roots 引用链的信息，一般可以比较准确地定位到这些对象创建的位置，进而找出产生内存泄漏的代码的具体位置。
* **如果不是内存泄漏**，换句话说就是**内存中的对象确实都是必须存活的**，那就**应当检查 Java 虚拟机的堆参数（-Xmx 与 -Xms）设置，与机器的内存对比，看看是否还有向上调整的空间**。**再从代码上检查是否存在某些对象生命周期过长、持有状态时间过长、存储结构设计不合理等情况，尽量减少程序运行期的内存消耗。**

## 虚拟机栈和本地方法栈溢出

> 由于 HotSpot 虚拟机中并不区分虚拟机栈和本地方法栈，因此对于 HotSpot 虚拟机来说，-Xoss 参数（设置本地方法栈大小）虽然存在，但实际上是没有任何效果的，栈容量只能由 -Xss 参数来设定。

关于虚拟机栈和本地方法栈，在《Java 虚拟机规范》中描述了两种异常：

1. 如果线程请求的栈深度大于虚拟机所允许的最大深度，将抛出 StackOverflowError 异常。
2. 如果虚拟机的栈内存允许动态扩展，当扩展栈容量无法申请到足够的内存时，将抛出 OutOfMemoryError 异常。

> 《Java 虚拟机规范》明确允许 Java 虚拟机实现自行选择是否支持栈的动态扩展，而 **HotSpot 虚拟机的选择是不支持扩展，**所以<mark style="color:blue;">**除非在创建线程申请内存时就因无法获得足够内存而出现 OutOfMemoryError 异常，否则在线程运行时是不会因为扩展而导致内存溢出的，只会因为栈容量无法容纳新的栈帧而导致StackOverflowError异常。**</mark>

### StackOverflowError

<details>

<summary><mark style="color:purple;">实验类</mark></summary>

```java
/**
 * VM Args：-Xss128k
 *
 * @author Zhang B H
 * @create 2024-01-15 21:34
 */
public class JavaVMStackSOF {
    private int stackLength = 1;

    public void stackLeak() {
        stackLength++;
        stackLeak();
    }

    public static void main(String[] args) throws Throwable {
        JavaVMStackSOF oom = new JavaVMStackSOF();
        try {
            oom.stackLeak();
        } catch (Throwable e) {
            System.out.println("stack length:" + oom.stackLength);
            throw e;
        }
    }
}
```

</details>

异常信息：

```java
Exception in thread "main" java.lang.StackOverflowError
```

### OutOfMemoryError

<details>

<summary><mark style="color:purple;">实验类（没有32位系统）</mark></summary>

```java
/**
 * VM Args：-Xss2M（这时候不妨设大些，请在32位系统下运行）
 *
 * @author Zhang B H
 * @create 2024-01-15 21:40
 */
public class JavaVMStackOOM {
    private void dontStop() {
        long unused1, unused2, unused3, unused4, unused5,
                unused6, unused7, unused8, unused9, unused10,
                unused11, unused12, unused13, unused14, unused15,
                unused16, unused17, unused18, unused19, unused20,
                unused21, unused22, unused23, unused24, unused25,
                unused26, unused27, unused28, unused29, unused30,
                unused31, unused32, unused33, unused34, unused35,
                unused36, unused37, unused38, unused39, unused40,
                unused41, unused42, unused43, unused44, unused45,
                unused46, unused47, unused48, unused49, unused50,
                unused51, unused52, unused53, unused54, unused55,
                unused56, unused57, unused58, unused59, unused60,
                unused61, unused62, unused63, unused64, unused65,
                unused66, unused67, unused68, unused69, unused70,
                unused71, unused72, unused73, unused74, unused75,
                unused76, unused77, unused78, unused79, unused80,
                unused81, unused82, unused83, unused84, unused85,
                unused86, unused87, unused88, unused89, unused90,
                unused91, unused92, unused93, unused94, unused95,
                unused96, unused97, unused98, unused99, unused100;
        unused1 = unused2 = unused3 = unused4 = unused5 = 0;
        unused6 = unused7 = unused8 = unused9 = unused10 = 0;
        unused11 = unused12 = unused13 = unused14 = unused15 = 0;
        unused16 = unused17 = unused18 = unused19 = unused20 = 0;
        unused21 = unused22 = unused23 = unused24 = unused25 = 0;
        unused26 = unused27 = unused28 = unused29 = unused30 = 0;
        unused31 = unused32 = unused33 = unused34 = unused35 = 0;
        unused36 = unused37 = unused38 = unused39 = unused40 = 0;
        unused41 = unused42 = unused43 = unused44 = unused45 = 0;
        unused46 = unused47 = unused48 = unused49 = unused50 = 0;
        unused51 = unused52 = unused53 = unused54 = unused55 = 0;
        unused56 = unused57 = unused58 = unused59 = unused60 = 0;
        unused61 = unused62 = unused63 = unused64 = unused65 = 0;
        unused66 = unused67 = unused68 = unused69 = unused70 = 0;
        unused71 = unused72 = unused73 = unused74 = unused75 = 0;
        unused76 = unused77 = unused78 = unused79 = unused80 = 0;
        unused81 = unused82 = unused83 = unused84 = unused85 = 0;
        unused86 = unused87 = unused88 = unused89 = unused90 = 0;
        unused91 = unused92 = unused93 = unused94 = unused95 = 0;
        unused96 = unused97 = unused98 = unused99 = unused100 = 0;
        while (true) {
        }
    }

    public void stackLeakByThread() {
        while (true) {
            Thread thread = new Thread(() -> dontStop());
            thread.start();
        }
    }

    public static void main(String[] args) {
        JavaVMStackOOM oom = new JavaVMStackOOM();
        oom.stackLeakByThread();
    }
}
```

</details>

异常信息：

```java
Exception in thread "main" java.lang.OutOfMemoryError: unable to create native thread
```

> <mark style="color:blue;">**操作系统分配给每个进程的内存是有限制的**</mark>，譬如 32 位 Windows 系统中单个进程的最大内存限制为 2GB。
>
> HotSpot 虚拟机提供了参数可以控制 Java 堆和方法区这两部分的内存的最大值，那剩余的内存即为 2GB（操作系统限制）**减去最大堆容量**，再**减去最大方法区容量**，由于程序计数器消耗内存很小，可以忽略掉，如果**把直接内存和虚拟机进程本身耗费的内存也去掉**的话，**剩下的内存就由虚拟机栈和本地方法栈来分配了**。
>
> <mark style="color:blue;">**因此为每个线程分配到的栈内存越大，可以建立的线程数量自然就越少，建立线程时就越容易把剩下的内存耗尽。**</mark>

> 如果使用 HotSpot 虚拟机默认参数，栈深度在大多数情况下到达 1000\~2000 是完全没有问题的，对于正常的方法调用（包括不能做尾递归优化的递归调用），这个深度应该完全够用了。

<mark style="color:blue;">**如果是建立过多线程导致的内存溢出，在不能减少线程数量或者更换 64 位虚拟机的情况下，就只能通过减少最大堆和减少栈容量来换取更多的线程。**</mark>这种通过“减少内存”的手段来解决内存溢出的方式，如果没有这方面的处理经验，一般比较难以想到，这一点需要在开发 32 位系统的多线程应用时稍加注意。

## 方法区和运行时常量池溢出

在 JDK 6 或更早之前的 HotSpot 虚拟机中，常量池都是分配在永久代中，可以通过 -XX:PermSize 和 -XX:MaxPermSize 限制永久代的大小，进而间接限制其中常量池的容量。

异常信息：

```java
Exception in thread "main" java.lang.OutOfMemoryError: PermGen space
```

**运行时常量池溢出时，在 OutOfMemoryError 异常后面跟随的提示信息是 “PermGen space”。**

> HotSpot 提供了一些参数作为元空间的防御措施，主要包括：
>
> * **-XX:MaxMetaspaceSize：**设置元空间最大值，默认是 -1，即不限制，或者说只受限于本地内存大小。
> * **-XX:MetaspaceSize：**指定元空间的初始空间大小，以字节为单位，达到该值就会触发垃圾收集进行类型卸载，同时收集器会对该值进行调整：
>   * 如果释放了大量的空间，就适当降低该值；
>   * 如果释放了很少的空间，那么在不超过 -XX:MaxMetaspaceSize 的情况下，适当提高该值。

## 本机直接内存溢出

直接内存（Direct Memory）的容量大小可通过 **-XX:MaxDirectMemorySize** 参数来指定，如果不指定，则默认与 Java 堆最大值（由 -Xmx 指定）一致。

<details>

<summary><mark style="color:purple;">实验类</mark></summary>

```java
/**
 * VM Args：-Xmx20M -XX:MaxDirectMemorySize=10M
 *
 * @author Zhang B H
 * @create 2024-01-15 22:00
 */
public class DirectMemoryOOM {
    private static final int _1MB = 1024 * 1024;

    public static void main(String[] args) throws Exception {
        Field unsafeField = Unsafe.class.getDeclaredFields()[0];
        unsafeField.setAccessible(true);
        Unsafe unsafe = (Unsafe) unsafeField.get(null);
        while (true) {
            unsafe.allocateMemory(_1MB);
        }
    }
}
```

</details>

异常信息：

```java
Exception in thread "main" java.lang.OutOfMemoryError
```

<mark style="color:blue;">**由直接内存导致的内存溢出，一个明显的特征是在 Heap Dump 文件中不会看见有什么明显的异常情况，如果发现内存溢出之后产生的 Dump 文件很小，而程序中又直接或间接使用了 DirectMemory（典型的间接使用就是 NIO），那就可以考虑重点检查一下直接内存方面的原因了。**</mark>
