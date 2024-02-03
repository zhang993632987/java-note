# synchronized关键字

**synchronized 关键字经过 Javac 编译之后，会在同步块的前后分别形成 **<mark style="color:blue;">**monitorenter**</mark>** 和 **<mark style="color:blue;">**monitorexit**</mark>** 这两个字节码指令。**这两个字节码指令都需要一个 reference 类型的参数来指明要锁定和解锁的对象：

* **如果 Java 源码中的 synchronized 明确指定了对象参数，那就以这个对象的引用作为 reference；**
* **如果没有明确指定，那将根据 synchronized 修饰的方法类型（如实例方法或类方法），来决定是取代码所在的对象实例还是取类型对应的 Class 对象来作为线程要持有的锁。**

根据《Java 虚拟机规范》的要求，在执行 monitorenter 指令时，首先要去尝试获取对象的锁。如果这个对象没被锁定，或者当前线程已经持有了那个对象的锁，就把锁的计数器的值增加一，而在执行 monitorexit 指令时会将锁计数器的值减一。一旦计数器的值为零，锁随即就被释放了。如果获取对象锁失败，那当前线程就应当被阻塞等待，直到请求锁定的对象被持有它的线程释放为止。
