# Java 容器

## Collection 和 Collections  <a href="#collection-he-collections-you-shen-me-qu-bie" id="collection-he-collections-you-shen-me-qu-bie"></a>

* **Collection 是一个接口**，它提供了对集合对象进行基本操作的通用接口方法，所有集合都是它的子类，比如 List、Set 等。
* **Collections 是一个工具类**，包含了很多静态方法、不能被实例化。该类是 Collection 的工具集，包含许多十分有用的方法。

## List

List 是一个有序队列，在 Java 中有两种实现方式：

* **ArrayList** 基于**数组**，存储空间是连续的；**LinkedList** 基于**双向链表**实现，存储空间是不连续的。
* 对于**随机访问** get 和 set ，ArrayList 要优于 LinkedList；而对于**新增和删除**操作 add 和 remove，LinkedList 则要优于 ArrayList。

> Vector 和 ArrayList 类似，但它是线程安全的。

## Map <a href="#id-2-2-map" id="id-2-2-map"></a>

* **TreeMap** 基于红黑树实现。
*   **HashMap** 1.8 基于数组+链表+红黑树。

    > 在 Java 8 中，当链表中的元素达到了 8 个时，会将链表转换为红黑树。
* **HashTable** 和 HashMap 类似，但它是线程安全的。
* **LinkedHashMap 使用双向链表来维护元素的顺序**，顺序为插入顺序或者最近最少使用（LRU）顺序。

## Set <a href="#jian-shu-java-de-set" id="jian-shu-java-de-set"></a>

Set 继承于 Collection 接口，表示一个**不允许出现重复元素，并且无序的集合**。

Java 中存在三种 Set 实现：

* **HashSet** 通过 HashMap 实现，**HashMap 的 Key 即 HashSet 存储的元素**。判断元素是否相同时，先比较 hashCode，如果 hashCode 相同再调用 equals 方法进行比较，查询的时间复杂度为 O(1)。
* **LinkedHashSet** 继承自 HashSet，通过 LinkedHashMap 实现，**使用双向链表维护元素插入顺序**。
* **TreeSet** 通过 TreeMap 实现的，底层数据结构是**红黑树**，添加元素到集合时按照比较规则将其插入到合适的位置，保证插入后的集合仍然有序。查询的时间复杂度是 O(logn)。

## TreeMap <a href="#jian-shu-java-de-treemap" id="jian-shu-java-de-treemap"></a>

**TreeMap 是底层利用红黑树实现的 Map 结构**，底层实现是一棵平衡的**排序二叉树**，由于<mark style="color:blue;">**红黑树的插入、删除、查找的时间复杂度都为 O(logN)**</mark>，所以性能上低于哈希表。但是哈希表无法提供键值对的有序输出，<mark style="color:blue;">**红黑树可以按照键值的大小顺序进行遍历。**</mark>

{% hint style="warning" %}
**只有确实存在“需要按照 key 的大小顺序依次遍历一个 Map”的需求时，才应该使用 TreeMap，其他情况均应使用 HashMap。**
{% endhint %}

## **WeakHashMap**

WeakHashMap 内部是通过**弱引用**来管理 entry 的，因此 WeakHashMap 里的 entry 可能会被 GC 自动删除，即使程序员没有调用 remove() 或者 clear() 方法。

**WeakHashMap 的这个特点特别适用于需要缓存的场景**。对象缓存命中可以提高系统效率，但缓存 MISS 也不会造成错误，因为可以通过计算重新得到。

## fail-fast 和 fail-safe 迭代器 <a href="#failfast-he-failsafe-die-dai-qi-de-qu-bie-shi-shen-me" id="failfast-he-failsafe-die-dai-qi-de-qu-bie-shi-shen-me"></a>

* **fail-fast** 直接在容器上进行遍历，**在遍历过程中，一旦发现容器中的数据被修改，就会立刻抛出 ConcurrentModificationException 异常**从而导致遍历失败。常见的使用 fail-fast 方式的容器有 HashMap 和 ArrayList 等。
* **fail-safe** 这种遍历基于容器的一个克隆。**因此对容器中的内容修改不影响遍历**。常见的使用 fail-safe 方式遍历的容器有 CopyOnWriteArrayList。
