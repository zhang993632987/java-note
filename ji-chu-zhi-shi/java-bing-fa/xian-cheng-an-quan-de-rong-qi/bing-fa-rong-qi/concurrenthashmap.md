# ConcurrentHashMap

> 数组 + 链表 + 红黑树，CAS

**ConcurrentHashMap** 使用一种**粒度更细的加锁机制**来实现更大程度的共享，这种机制称为<mark style="color:blue;">**分段锁（Lock Striping），**</mark>在这种机制中：

* 任意数量的读取线程可以并发地访问 Map
* 执行读取操作的线程和执行写入操作的线程可以并发地访问 Map
* 一定数量的写入线程可以并发地修改 Map

**ConcurrentHashMap 带来的结果是，在并发访问环境下将实现更高的吞吐量，而在单线程环境中只损失非常小的性能。**

{% hint style="success" %}
<mark style="color:blue;">**ConcurrentHashMap 的迭代器不会抛出 ConcurrentModificationException。**</mark>

ConcurrentHashMap 返回的迭代器具有<mark style="color:blue;">**弱一致性（Weakly Consistent）**</mark>，而并非“及时失败”。**弱一致性的迭代器可以容忍并发的修改，当创建迭代器时会遍历已有的元素，并可以（但是不保证）在迭代器被构造后将修改操作反映给容器。**
{% endhint %}

{% hint style="warning" %}
## <mark style="color:orange;">注意</mark>

对于一些**需要在整个 Map 上进行计算的方法**，例如 size 和 isEmpty，**这些方法的语义被略微减弱了以反映容器的并发特性。由于 size 返回的结果在计算时可能已经过期了，它实际上只是一个估计值，因此**<mark style="color:orange;">**允许 size 返回一个近似值而不是一个精确值**</mark>**。**

虽然这看上去有些令人不安，但**事实上 size 和 isEmpty 这样的方法在并发环境下的用处很小，因为它们的返回值总在不断变化**。因此，这些操作的需求被弱化了，以换取对其他更重要操作的性能优化，包括 get、put、containsKey 和 remove 等。
{% endhint %}

<details>

<summary><mark style="color:purple;">ConcurrentHashMap</mark></summary>

<pre class="language-java"><code class="lang-java"><strong>    // volatile 和 final 用于保证可见性
</strong><strong>    volatile Node&#x3C;K,V>[] table;
</strong><strong>    
</strong><strong>    static class Node&#x3C;K,V> implements Map.Entry&#x3C;K,V> {
</strong>        final int hash;
        final K key;
        volatile V val;
        volatile Node&#x3C;K,V> next;
        ...    
    }
    
    // get 方法未使用锁
    public V get(Object key) {
        Node&#x3C;K,V>[] tab; 
        Node&#x3C;K,V> e, p; 
        int n, eh; 
        K ek;
        int h = spread(key.hashCode());
        if ((tab = table) != null &#x26;&#x26; (n = tab.length) > 0 &#x26;&#x26;
            (e = tabAt(tab, (n - 1) &#x26; h)) != null) {
            if ((eh = e.hash) == h) {
                if ((ek = e.key) == key || (ek != null &#x26;&#x26; key.equals(ek)))
                    return e.val;
            }
            else if (eh &#x3C; 0)
                return (p = e.find(h, key)) != null ? p.val : null;
            while ((e = e.next) != null) {
                if (e.hash == h &#x26;&#x26;
                    ((ek = e.key) == key || (ek != null &#x26;&#x26; key.equals(ek))))
                    return e.val;
            }
        }
        return null;
    }
</code></pre>

<pre class="language-java" data-overflow="wrap"><code class="lang-java">
<strong>    public V put(K key, V value) {
</strong>        return putVal(key, value, false);
    }
    
    /**
     * 在 putVal 方法中使用了两种同步机制：
     *    1. CAS：使用 CAS 在 Node 数组中增加节点
     *    2. 分段锁：每一个哈希桶中的列表，都使用头节点作为锁对象
     */
    final V putVal(K key, V value, boolean onlyIfAbsent) {
        ...
        for (Node&#x3C;K,V>[] tab = table;;) {
            ...
            else if ((f = tabAt(tab, i = (n - 1) &#x26; hash)) == null) {
                if (casTabAt(tab, i, null, new Node&#x3C;K,V>(hash, key, value)))
                    break;           // no lock when adding to empty bin
            }
            ...
            else {
                V oldVal = null;
                synchronized (f) {
                   ...
                }
                ...
            }
        }
        addCount(1L, binCount);
        return null;
    }
</code></pre>

<pre class="language-java"><code class="lang-java"><strong>    public int size() {
</strong>        long n = sumCount();
        return ((n &#x3C; 0L) ? 0 :
                (n > (long)Integer.MAX_VALUE) ? Integer.MAX_VALUE :
                (int)n);
    }
    
    final long sumCount() {
        CounterCell[] cs = counterCells;
        long sum = baseCount;
        if (cs != null) {
            for (CounterCell c : cs)
                if (c != null)
                    sum += c.value;
        }
        return sum;
    }
    
    /**
     * A padded cell for distributing counts.  Adapted from LongAdder
     * and Striped64.  See their internal docs for explanation.
     */
    @jdk.internal.vm.annotation.Contended static final class CounterCell {
        volatile long value;
        CounterCell(long x) { value = x; }
    }
</code></pre>

</details>

<details>

<summary><mark style="color:purple;"><strong>JDK1.7 实现</strong></mark></summary>

在 JDK1.5 \~ 1.7 版本中，Java 使用了**分段锁**机制实现 ConcurrentHashMap：

* ConcurrentHashMap 在对象中保存了一个 Segment 数组，从而整个 Hash 表划分为多个分段；
* 而每个 Segment 元素，它通过继承 ReentrantLock 来进行加锁，所以每次需要加锁的操作锁住的是一个 segment，这样只要保证每个 Segment 是线程安全的，也就实现了全局的线程安全；

在执行 put 操作时首先根据 hash 算法定位到元素属于哪个 Segment，然后对该 Segment 加锁即可。Segment 数量默认是 16，理论上这个时候最多可以同时支持 16 个线程并发写，只要它们的操作分别分布在不同的 Segment 上。

![](../../../../.gitbook/assets/java-thread-x-concurrent-hashmap-1.png)

</details>
