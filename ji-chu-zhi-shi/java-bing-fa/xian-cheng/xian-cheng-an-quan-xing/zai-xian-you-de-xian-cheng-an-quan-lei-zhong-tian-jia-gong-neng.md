# 在现有的线程安全类中添加功能

## <mark style="color:blue;">**修改原始的类**</mark>

要在已有的线程安全类中添加一个新的原子操作，最安全的方法是**修改原始的类。**

**不足之处：**这通常无法做到，因为可能无法访问或修改类的源代码。

## <mark style="color:blue;">**扩展类机制**</mark>

如果在设计这个线程安全类时考虑了可扩展性，那么可以通过**继承**来添加原子操作。

比如以下代码中的 BetterVector 扩展了 Vector 类，并添加了一个新的同步方法 putIfAbsent：

```java
public class BetterVector<E> extends Vector<E> {
    public synchronized boolean putIfAbsent(E e) {
        boolean absent = !contains(e);
        if (absent)
            add(e);
        return absent;
    }
}
```

**不足之处：**如果父类改变了同步策略并选择了不同的锁来保护它的状态变量，那么子类会被破坏。

## <mark style="color:blue;">**客户端加锁机制**</mark>

扩展类的功能，但并不是扩展类本身，而是将扩展代码放入一个“辅助类”中。

如下所示：

```java
public class ListHelper<E> {
    public List<E> list = Collections.synchronizedList(new ArrayList<E>());
    ...
    public boolean putIfAbsent(E e) {
        synchronized(list) {
            boolean absent = !contains(e);
            if (absent)
                add(e);
            return absent;
        }
    }
}
```

**不足之处：客户端加锁机制与扩展类机制一样，将派生类的行为与基类的实现耦合在了一起。**如果基类改变了同步策略并选择了不同的锁来保护它的状态变量，那么“辅助类”会被破坏。

## <mark style="color:blue;">**组合**</mark>

```java
public class ImprovedList<E> implements List<E> {
    private final List<E> list;
    
    public ImprovedList(List<E> list) {
        this.list = list;
    }
    
    public synchronized putIfAbsent(E e) {
        boolean absent = !list.contains(e);
        if (absent)
            list.add(e);
        return absent;
    }
    
    public synchronized void clear() { list.clear(); }
    ...
} 
```

ImprovedList 通过自身的内置锁增加了一层额外的加锁。它并不关心底层的 List 是否是线程安全的，即使 List 不是线程安全的或者修改了它的加锁实现，ImprovedList 也会提供一致的加锁机制来实现线程安全性。
