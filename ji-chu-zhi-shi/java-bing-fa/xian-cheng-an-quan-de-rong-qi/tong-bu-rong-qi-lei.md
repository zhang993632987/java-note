# 同步容器类

同步容器类包括 <mark style="color:blue;">**Vector**</mark> 和 <mark style="color:blue;">**Hashtable**</mark>，二者是早期 JDK 的一部分，此外还包括在 JDK l.2 中添加的一些功能相似的类，这些同步的封装器类是由 **Collections.synchronizedXxx** 等工厂方法创建的。**这些类实现线程安全的方式是：将它们的状态封装起来，并对每个公有方法都进行同步，使得每次只有一个线程能访问容器的状态。**

## 迭代器与 ConcurrentModificationException

在设计同步容器类的迭代器时并没有考虑到并发修改的问题，并且它们表现出的行为是“**及时失败**”（fail-fast）的。这意味着，**当它们发现容器在迭代过程中被修改时，就会抛出一个ConcurrentModificationException 异常。**

这种“及时失败”的迭代器并不是一种完备的处理机制，而只是“善意地”捕获并发错误，因此只能作为并发问题的预警指示器。<mark style="color:blue;">**它们采用的实现方式是，将计数器的变化与容器关联起来：如果在迭代期间计数器被修改，那么 hasNext 或 next 将抛出 ConcurrentModificationException。**</mark>然而，这种检查是在没有同步的情况下进行的，因此可能会看到失效的计数值，而迭代器可能并没有意识到已经发生了修改。这是一种设计上的权衡，从而降低并发修改操作的检测代码对程序性能带来的影响。

{% hint style="success" %}
在单线程代码中也可能抛出 **ConcurrentModificationException** 异常。<mark style="color:blue;">**当对象直接从容器中删除而不是通过 Iterator.remove 来删除时，就会抛出这个异常。**</mark>
{% endhint %}

{% hint style="warning" %}
## <mark style="color:orange;">注意</mark>

容器的 **hashCode、equals、containsAll**、**removeAll** 和 **retainAll** 等方法，以及把容器作为参数的构造函数，都会对容器进行迭代。

<mark style="color:orange;">**所有这些间接的迭代操作都可能抛出 ConcurrentModificationException。**</mark>
{% endhint %}

<details>

<summary><mark style="color:purple;">Vector</mark></summary>

**在 Vector 中的 modCount 类似于“代”的概念，任何修改操作都会产生一个 modCount 自增，而 modCount 的值并不含有元素数量的含义。**

```java
public class Vector<E> {
    protected transient int modCount = 0;
    
    public boolean addAll(Collection<? extends E> c) {
        modCount++;
        ...
    }
    
    public synchronized E remove(int index) {
        modCount++;
        ...
    }

    ...
    
    private class Itr implements Iterator<E> {
        int expectedModCount = modCount;

        public E next() {
            synchronized (Vector.this) {
                checkForComodification();
                ...
            }
        }

        public void remove() {
            synchronized (Vector.this) {
                checkForComodification();
                ...
            }
            ...
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }
}
```

</details>
