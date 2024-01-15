# 永久代与元空间

在 Java 8 中，永久代被元数据区（也叫作元空间）取代。元数据区的作用和永久代类似，二者最大的区别在于：**元数据区并没有使用虚拟机的内存**，而是**直接使用操作系统的本地内存**。因此，元空间的大小不受 JVM 内存的限制，只和操作系统的内存有关。

**在 Java 8 中，JVM 将类的元数据放入本地内存（Native Memory）中，将常量池和类的静态变量放入Java 堆中**，这样 JVM 能够加载多少元数据信息就不再由 JVM 的最大可用内存（MaxPermSize）空间决定，而由操作系统的实际可用内存空间决定。

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

这段代码在 JDK 1.6 中运行，会得到两个 false，而在 JDK 1.7 中运行，会得到一个 true 和一个 false。

> **String::intern()** 是一个本地方法，它**的作用是如果字符串常量池中已经包含一个等于此 String 对象的字符串，则返回代表池中这个字符串的 String 对象的引用；否则，会将此 String 对象包含的字符串添加到常量池中，并且返回此 String 对象的引用。**

产生差异的原因是：

* **在 JDK 1.6 中，intern() 方法会把首次遇到的字符串实例复制到永久代中，返回的也是永久代中这个字符串实例的引用**，而由 StringBuilder 创建的字符串实例在 Java 堆上，所以必然不是同一个引用，将返回 false。
* **JDK 1.7 的 intern() 实现不会再复制实例，只是在常量池中记录首次出现的实例引用，因此 intern() 返回的引用和由 StringBuilder 创建的那个字符串实例是同一个**。对 str2 比较返回 false 是因为“java”这个字符串在执行 StringBuilder.toString() 之前已经出现过，字符串常量池中已经有它的引用了，不符合“首次出现”的原则，而“计算机软件”这个字符串则是首次出现的，因此返回 true。
