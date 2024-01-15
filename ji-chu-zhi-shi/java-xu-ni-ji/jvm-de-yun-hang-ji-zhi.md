# JVM 的运行机制

JVM（Java Virtual Machine）是用于运行 Java 字节码的虚拟机，包括一个<mark style="color:blue;">**类加载器子系统（Class Loader SubSystem）**</mark>、<mark style="color:blue;">**运行时数据区（Runtime Data Area）**</mark>、<mark style="color:blue;">**执行引擎**</mark>和<mark style="color:blue;">**本地接口库（Native Interface Library）**</mark>。

其中：

* **类加载器子系统**用于将编译好的 Class 文件加载到 JVM 中；
* **运行时数据区**用于存储在 JVM 运行过程中产生的数据，包括<mark style="color:blue;">**程序计数器**</mark>、<mark style="color:blue;">**方法区**</mark>、<mark style="color:blue;">**本地方法区**</mark>、<mark style="color:blue;">**虚拟机栈**</mark>和<mark style="color:blue;">**虚拟机堆**</mark>；
* **执行引擎**包括<mark style="color:blue;">**即时编译器**</mark>和<mark style="color:blue;">**垃圾回收器**</mark>
  * **即时编译器**用于将 Java 字节码编译成具体的机器码
  * **垃圾回收器**用于回收在运行过程中不再使用的对象
