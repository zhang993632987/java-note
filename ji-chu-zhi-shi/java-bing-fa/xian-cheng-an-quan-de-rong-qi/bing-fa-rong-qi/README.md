# 并发容器

在Java 5.0中增加了**ConcurrentHashMap**，用来替代同步且基于散列的Map，以及**CopyOnWriteArrayList**，用于在遍历操作为主要操作的情况下代替同步的List。

Java 5.0增加了两种新的容器类型：Queue和BlockingQueue。它提供了几种实现，包括：**ConcurrentLinkedQueue**，这是一个传统的先进先出队列，以及**PriorityQueue**，这是一个（非并发的）优先队列。

* **Queue**上的操作不会阻塞，**如果队列为空，那么获取元素的操作将返回空值。**
* **BlockingQueue**扩展了Queue，增加了**可阻塞的插入和获取等操作**。
  * 如果队列为空，那么获取元素的操作将一直阻塞，直到队列中出现一个可用的元素。
  * 如果队列已满（对于有界队列来说），那么插入元素的操作将一直阻塞，直到队列中出现可用的空间。

Java 6引入了**ConcurrentSkipListMap**和**ConcurrentSkipListSet**，分别作为同步的**SortedMap**和**SortedSet**的并发替代品。
