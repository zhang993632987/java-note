# 安全发布

## 发布与逸出

“**发布（Publish）**”一个对象的意思是指：**使对象能够在当前作用域之外的代码中使用**。**当某个不应该发布的对象被发布时**，这种情况就被称为**逸出。**

> <mark style="color:orange;">**当发布一个对象时，在该对象的非私有域中引用的所有对象同样会被发布。**</mark>

## 安全的对象构造过程

当且仅当对象的构造函数返回时，对象才处于可预测的和一致的状态。因此，当从对象的构造函数中发布对象时，只是发布了一个尚未构造完成的对象，即使发布对象的语句位于构造函数的最后一行也是如此。

> <mark style="color:orange;">**不要在构造函数中使 this 引用逸出。**</mark>
>
> <mark style="color:orange;">**在构造函数中调用一个可改写的实例方法时（既不是私有方法，也不是 final 方法），同样会导致 this 引用在构造过程中逸出。**</mark>

> <mark style="color:orange;">**在构造函数中创建线程并没有错误，但最好不要立即启动它，而是通过一个 start 或 initialize 方法来启动。**</mark>

## 不可变对象与初始化安全性

<mark style="color:blue;">**任何线程都可以在不需要额外同步的情况下安全地访问不可变对象，即使在发布这些对象时没有使用同步。**</mark>

* 这种保证还将延伸到被正确创建对象中所有 final 类型的域。在没有额外同步的情况下，也可以安全地访问 final 类型的域。
* 然而，如果 final 类型的域所指向的是可变对象，那么在访问这些所指向的对象的状态时仍然需要同步。

## 安全发布的常用模式

**要安全地发布一个对象，对象的引用以及对象的状态必须同时对其他线程可见**。一个**正确构造的对象**可以通过以下方式来安全地发布：

* <mark style="color:blue;">**在静态初始化函数中初始化一个对象引用**</mark>
* <mark style="color:blue;">**将对象的引用保存到 volatile 类型的域，或者 AtomicReference 对象中**</mark>
* <mark style="color:blue;">**将对象的引用保存到某个正确构造对象的 final 类型域中**</mark>
* <mark style="color:blue;">**将对象的引用保存到一个由锁保护的域中**</mark>

所有的安全发布机制都能确保，当对象的引用对所有访问该对象的线程可见时，对象发布时的状态对于所有线程也将是可见的。

对象的发布需求取决于它的可变性：

* <mark style="color:blue;">**不可变对象可以通过任意机制来发布**</mark>
* <mark style="color:blue;">**事实不可变对象必须通过安全发布方式来发布**</mark>
* <mark style="color:blue;">**可变对象必须通过安全发布方式来发布，并且必须是线程安全的或者由某个锁保护起来**</mark>

> ## 事实不可变对象
>
> 如果对象从技术上来看是可变的，但其状态在发布后不会再改变，那么把这种对象称为**“事实不可变对象（Effectively Immutable Object）”**。
>
> **在没有额外同步的情况下，任何线程都可以安全地使用**<mark style="color:orange;">**被安全发布的**</mark>**事实不可变对象。**

> ## 可变对象
>
> **对于可变对象，安全发布只能确保“发布当时”状态的可见性。**
>
> 因此，对于可变对象，不仅在发布对象时需要使用同步，在每次对象访问时同样需要使用同步来确保后续修改操作的可见性。
