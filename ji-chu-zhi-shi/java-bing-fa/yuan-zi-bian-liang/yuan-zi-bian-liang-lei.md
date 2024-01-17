# 原子变量类

共有 12 个原子变量类，可分为 4 组：

* <mark style="color:blue;">**标量类（Scalar）**</mark>
  * AtomicBoolean
  * AtomicInteger
  * AtomicLong
* <mark style="color:blue;">**更新器类**</mark>
  * AtomicIntegerFieldUpdater
  * AtomicLongFieldUpdater
  * AtomicReferenceFieldUpdater
* <mark style="color:blue;">**数组类**</mark>
  * AtomicIntegerArray
  * AtomicLongArray
  * AtomicReferenceArray
* <mark style="color:blue;">**复合变量类**</mark>
  * AtomicReference
  * AtomicStampedReference
  * AtomicMarkableReferce

最常用的原子变量类是：**AtomicInteger**、**AtomicLong**、**AtomicBoolean** 以及 **AtomicReference**。

> 原子数组类中的元素可以实现原子更新。<mark style="color:blue;">**原子数组类为数组的元素提供了 volatile 类型的访问语义**</mark>，这是普通数组所不具备的特性——<mark style="color:orange;">**volatile 类型的数组仅在数组引用上具有 volatile 语义，而在其元素上则没有。**</mark>
