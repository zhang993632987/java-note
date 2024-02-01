# 先行发生（Happens-Before）原则

**先行发生是 Java 内存模型中定义的两项操作之间的偏序关系**，比如说操作 A 先行发生于操作 B，其实就是说在发生操作 B 之前，操作 A 产生的影响能被操作 B 观察到，“影响”包括修改了内存中共享变量的值、发送了消息、调用了方法等。

下面是 Java 内存模型下一些“天然的”先行发生关系，这些先行发生关系无须任何同步器协助就已经存在，可以在编码中直接使用。**如果两个操作之间的关系不在此列，并且无法从下列规则推导出来，则它们就没有顺序性保障，虚拟机可以对它们随意地进行重排序**。

* <mark style="color:blue;">**程序次序规则（Program Order Rule）**</mark>：**在一个线程内，按照控制流顺序，书写在前面的操作先行发生于书写在后面的操作。**注意，这里说的是控制流顺序而不是程序代码顺序，因为要考虑分支、循环等结构。
* <mark style="color:blue;">**管程锁定规则（Monitor Lock Rule）**</mark>：**一个 unlock 操作先行发生于后面对同一个锁的 lock 操作。**这里必须强调的是“**同一个锁**”，而“后面”是指**时间上的先后**。
* <mark style="color:blue;">**volatile 变量规则（Volatile Variable Rule）**</mark>：**对一个 volatile 变量的写操作先行发生于后面对这个变量的读操作**，这里的“后面”同样是指时间上的先后。
* <mark style="color:blue;">**线程启动规则（Thread Start Rule）**</mark>：**Thread 对象的 start() 方法先行发生于此线程的每一个动作。**
* <mark style="color:blue;">**线程终止规则（Thread Termination Rule）**</mark>：**线程中的所有操作都先行发生于对此线程的终止检测，**可以通过 Thread::join() 方法是否结束、Thread::isAlive() 的返回值等手段检测线程是否已经终止执行。
* <mark style="color:blue;">**线程中断规则（Thread Interruption Rule）**</mark>：**对线程 interrupt() 方法的调用先行发生于被中断线程的代码检测到中断事件的发生**，可以通过 Thread::interrupted() 方法检测到是否有中断发生。
* <mark style="color:blue;">**对象终结规则（Finalizer Rule）**</mark>：**一个对象的初始化完成（构造函数执行结束）先行发生于它的 finalize() 方法的开始。**
* <mark style="color:blue;">**传递性（Transitivity）**</mark>：**如果操作 A 先行发生于操作 B，操作 B 先行发生于操作 C，那就可以得出操作 A 先行发生于操作 C 的结论。**
