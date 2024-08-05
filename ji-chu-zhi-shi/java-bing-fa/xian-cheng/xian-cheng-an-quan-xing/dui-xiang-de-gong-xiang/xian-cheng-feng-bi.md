# 线程封闭

如果仅在单线程内访问数据，就不需要同步，这种技术被称为**线程封闭（Thread Confinement）**。

## 栈封闭

在栈封闭中，只能通过局部变量才能访问对象。

## ThreadLocal 类

ThreadLocal 类的使用方式如下：

```java
private static ThreadLocal<Connection> connectionHolder = new ThreadLocal<>() {
    public Connection initialValue() {
        return DriverManager.getConnection(DB_URL);  
    }
};

public static Connection getConnection() {
    return connectionHolder.get();
}
```

当某个线程第一次调用 ThreadLocal.get 方法时，就会调用 initialValue 来获取初始值。

<details>

<summary><mark style="color:purple;">ThreadLocal 原理</mark></summary>

从概念上看，可以将 ThreadLocal\<T> 视为包含了 Map\<Thread, T> 对象，其中保存了特定于该线程的值。但 ThreadLocal 的实现并非如此，这些特定于线程的值保存在 Thread 对象中，当线程终止后，这些值会作为垃圾回收。

{% code title="ThreadLocal.class" %}
```java
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}

public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        map.set(this, value);
    } else {
        createMap(t, value);
    }
}

void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```
{% endcode %}

<pre class="language-java" data-title="ThreadLocalMap.class"><code class="lang-java"><strong>static class Entry extends WeakReference&#x3C;ThreadLocal&#x3C;?>> {
</strong>    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal&#x3C;?> k, Object v) {
        super(k);
        value = v;
    }
}

private Entry[] table;

private Entry getEntry(ThreadLocal&#x3C;?> key) {
    int i = key.threadLocalHashCode &#x26; (table.length - 1);
    Entry e = table[i];
    if (e != null &#x26;&#x26; e.get() == key)
        return e;
    else
        return getEntryAfterMiss(key, i, e);
}

private void set(ThreadLocal&#x3C;?> key, Object value) {

    // We don't use a fast path as with get() because it is at
    // least as common to use set() to create new entries as
    // it is to replace existing ones, in which case, a fast
    // path would fail more often than not.

    Entry[] tab = table;
    int len = tab.length;
    int i = key.threadLocalHashCode &#x26; (len-1);

    for (Entry e = tab[i];
         e != null;
         e = tab[i = nextIndex(i, len)]) {
        ThreadLocal&#x3C;?> k = e.get();

        if (k == key) {
            e.value = value;
            return;
        }

        if (k == null) {
            replaceStaleEntry(key, value, i);
            return;
        }
    }

    tab[i] = new Entry(key, value);
    int sz = ++size;
    if (!cleanSomeSlots(i, sz) &#x26;&#x26; sz >= threshold)
        rehash();
}
</code></pre>

每个线程 Thread 类都有个属性 ThreadLocalMap，用来维护该线程的多个 ThreadLocal 变量，该 Map 是自定义实现的 Entry\[] 数组结构，并非继承自原生 Map 类。

在这里，Entry 是一个继承自 WeakReference\<ThreadLocal\<?>> 的类，它包含两个字段：

1. ThreadLocal\<?> k：一个对 ThreadLocal 对象的弱引用，通过这个引用，可以获取到ThreadLocal 对象本身。
2. Object v：与 ThreadLocal 关联的值。

**这里使用了 WeakReference，是为了防止内存泄漏：**

* **如果只使用强引用，当 ThreadLocal 没有被外部引用时，ThreadLocalMap 中的 Entry 对象可能会一直存在，导致内存泄漏。**
* **通过使用弱引用，一旦 ThreadLocal 被垃圾回收，与之相关的 Entry 也会被回收。使用弱引用可以防止长期存在的线程（通常使用了线程池）导致 ThreadLocal 无法回收造成内存泄漏。**

</details>
