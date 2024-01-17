# 非阻塞的链表（X）



<details>

<summary><mark style="color:purple;">Node</mark></summary>



</details>

在 ConcurrentLinkedQueue 中没有使用原子引用来表示每个 Node，而是使用普通的 volatile 类型引用，并通过基于反射的 AtomicReferenceFieldUpdater 来进行更新。

原子的域更新器类表示现有 volatile 域的一种基于反射的“视图”，从而能够在已有的 volatile 域上使用CAS。

* 在更新器类中没有构造函数，要创建一个更新器对象，可以调用 newUpdater 工厂方法，并制定类和域的名字。
* 域更新器类没有与某个特定的实例关联在一起，因而可以更新目标类的任意实例中的域。
* 更新器类提供的原子性保证比普通原子类更弱一些，因为无法保证底层的域不被直接修改——compareAndSet 以及其他算术方法只能确保其他使用原子域更新器方法的线程的原子性。

> **几乎在所有情况下，普通原子变量的性能都很不错，只有在很少的情况下才需要使用原子的域更新器。**
