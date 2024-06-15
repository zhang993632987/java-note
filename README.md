# Java 语言的特点

1. Java 为纯<mark style="color:blue;">**面向对象**</mark>的语言。
2. 具有<mark style="color:blue;">**平台无关**</mark>性。编译器把 Java 代码编译成平台无关的中间代码，然后在 JVM 上运行。
3. Java 程序是通过解释器进行<mark style="color:blue;">**解释执行**</mark>的，但部分商用虚拟机为了提高热点代码的执行效率，会利用<mark style="color:blue;">**即时编译**</mark>器将代码编译成平台无关的机器码，并进行各种优化。
4. Java 提供了很多<mark style="color:blue;">**内置类库**</mark>。如对多线程的支持，对网络通信的支持，最重要的一点是提供了垃圾回收器。
5. Java 具有较好的<mark style="color:blue;">**安全性**</mark>和<mark style="color:blue;">**健壮性**</mark>。Java 提供了异常处理和垃圾回收机制，去除了 C++ 中难以理解的指针特性。

## 面向对象的三大特性 <a href="#mian-xiang-dui-xiang-de-san-da-te-xing" id="mian-xiang-dui-xiang-de-san-da-te-xing"></a>

### <mark style="color:blue;">**封装**</mark>

* 利用抽象数据类型将数据和基于数据的操作封装在一起，使其构成一个不可分割的独立实体。
* 数据被保护在抽象数据类型的内部，尽可能地隐藏内部的细节，只保留一些对外接口使之与外部发生联系。
* 用户无需知道对象内部的细节，但可以通过对象对外提供的接口来访问该对象。

### <mark style="color:blue;">**继承**</mark>

对象的一个新类可以从现有的类中派生，派生类可以从它的基类那继承方法和实例变量，且派生类可以修改或新增新的方法使之更适合特殊的需求。

### <mark style="color:blue;">**多态**</mark>

允许不同类的对象对同一消息作出不同响应。不同对象调用相同方法即使参数也相同，最终表现行为是不一样的。
