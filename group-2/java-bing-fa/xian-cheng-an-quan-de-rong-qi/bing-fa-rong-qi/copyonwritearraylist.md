# CopyOnWriteArrayList

**CopyOnWriteArrayList**用于**替代同步List**，在某些情况下它提供了更好的并发性能，并且**在迭代期间不需要对容器进行加锁或复制。**（类似地，**CopyOnWriteArraySet**的作用是**替代同步Set**。）

<mark style="color:blue;">**“写入时复制（Copy-On-Write）”**</mark>指的是：**在每次修改时，都会创建并重新发布一个新的容器副本，从而实现可变性。**

* **“写入时复制”容器的迭代器保留一个指向底层基础数组的引用**，**由于它不会被修改，因此在对其进行同步时只需确保数组内容的可见性。**因此，多个线程可以同时对这个容器进行迭代，而不会彼此干扰或者与修改容器的线程相互干扰。
* **“写入时复制”容器返回的迭代器不会抛出ConcurrentModificationException，并且返回的元素与迭代器创建时的元素完全一致，而不必考虑之后修改操作所带来的影响。**

显然，每当修改容器时都会复制底层数组，这需要一定的开销，特别是当容器的规模较大时。<mark style="color:orange;">**仅当迭代操作远远多于修改操作时，才应该使用“写入时复制”容器。**</mark>这个准则很好地描述了许多**事件通知系统**：**在分发通知时需要迭代已注册监听器链表，并调用每一个监听器，在大多数情况下，注册和注销事件监听器的操作远少于接收事件通知的操作。**

<details>

<summary><mark style="color:purple;">CopyOnWriteArrayList</mark></summary>

```java
// volatile 的作用是保证读操作的可见性
private transient volatile Object[] array;

public boolean add(E e) {
    synchronized (lock) {
        Object[] es = this.array;
        int len = es.length;
        es = Arrays.copyOf(es, len + 1); # 复制数组
        es[len] = e; 
        this.array = a;
        return true;
    }
}

static final class COWIterator<E> implements ListIterator<E> {
    /** Snapshot of the array */
    private final Object[] snapshot;
    /** Index of element to be returned by subsequent call to next.  */
    private int cursor;

    COWIterator(Object[] es, int initialCursor) {
       cursor = initialCursor;
       snapshot = es;
    }
    
    public void remove() {
        throw new UnsupportedOperationException();
    }
    
    ...
}
```

</details>
