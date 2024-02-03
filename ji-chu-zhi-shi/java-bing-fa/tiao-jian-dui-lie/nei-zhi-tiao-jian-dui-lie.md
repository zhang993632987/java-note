# 内置条件队列

正如每个 Java 对象都可以作为一个锁，每个对象同样可以作为一个条件队列，并且 Object 中的 wait、 notify 和 notifyAll 方法就构成了内部条件队列的 API。<mark style="color:orange;">**对象的内置锁与其内部条件队列是相互关联的，要调用对象 X 中条件队列的任何一个方法，必须持有对象 X 上的锁。**</mark>这是因为“**等待由状态构成的条件**”与“**维护状态一致性**”这两种机制必须被紧密地绑定在一起：只有能对状态进行检查时，才能在某个条件上等待，并且只有能修改状态时，才能从条件等待中释放另一个线程。

<mark style="color:blue;">**Object.wait 会自动释放锁，并请求操作系统挂起当前线程，从而使其他线程能够获得这个锁并修改对象的状态。当被挂起的线程醒来时，它将在返回之前重新获取锁**</mark>**。**当线程从 wait 方法中被唤醒时，它在重新请求锁时不具有任何特殊的优先级，而要与任何其他尝试进入同步代码块的线程一起正常地在锁上进行竞争。

在条件等待中存在一种重要的三元关系，包括**加锁**、**wait 方法**和一个**条件谓词**。**在条件谓词中包含多个状态变量，而状态变量由一个锁来保护，因此在测试条件谓词之前必须先持有这个锁。**<mark style="color:orange;">**锁对象与条件队列对象（即调用 wait 和 notify 等方法所在的对象）必须是同一个对象。**</mark>

## 通知

在条件队列 API 中有两个发出通知的方法，即 **notify** 和 **notifyAll**。无论调用哪一个，都必须持有与条件队列对象相关联的锁。

* <mark style="color:blue;">**在调用 notify 时，JVM 会从这个条件队列上等待的多个线程中选择一个来唤醒，而调用 notifyAll 则会唤醒所有在这个条件队列上等待的线程。**</mark>
* **由于在调用 notify 或 notifyAll 时必须持有条件队列对象的锁**，而如果这些等待中线程此时不能重新获得锁，那么无法从 wait 返回，**因此**<mark style="color:orange;">**发出通知的线程应该尽快地释放锁，从而确保正在等待的线程尽可能快地解除阻塞。**</mark>

{% hint style="success" %}
只有同时满足以下两个条件时，才能用单一的 notify 而不是notifyAll：

* 所有等待线程的类型都相同。**只有一个条件谓词与条件队列相关，并且每个线程在从 wait 返回后将执行相同的操作。**
* 单进单出。**在条件变量上的每次通知，最多只需要唤醒一个线程来执行。**
{% endhint %}

## 状态依赖方法的标准形式

**内置条件队列可以与多个条件谓词一起使用。**<mark style="color:orange;">**当一个线程由于调用 notifyAll 而醒来时，并不意味该线程正在等待的条件谓词已经变成真了。**</mark>因此，每当线程从 wait 中唤醒时，都必须再次测试条件谓词，如果条件谓词不为真，那么就继续等待（或者失败）。<mark style="color:orange;">**由于线程在条件谓词不为真的情况下也可以反复地醒来，因此必须在一个循环中调用 wait，并在每次迭代中都测试条件谓词。**</mark>

下面的程序清单给出了条件等待的标准形式：

```java
void stateDependentMethod() throws InterruptedException {
    synchronized (lock) {
        while (!conditionPredicate())
            locak.wait();
        // 现在对象处于合适的状态
    }
}
```

当使用条件等待时（例如 Object.wait 或 Condition.await）：

* **通常都有一个条件谓词**——包括一些对象状态的测试，线程在执行前必须首先通过这些测试。
* **在调用 wait 之前测试条件谓词，并且从 wait 中返回时再次进行测试。**
* **在一个循环中调用 wait。**
* **确保使用与条件队列相关的锁来保护构成条件谓词的各个状态变量。**
* **当调用 wait、notify 或 notifyAll 等方法时，一定要持有与条件队列相关的锁。**
* **在检查条件谓词之后以及开始执行相应的操作之前，不要释放锁。**

## 示例

<details>

<summary><mark style="color:purple;">使用条件队列实现的有界缓存</mark></summary>

```java
public final class BoundedBuffer<T> {

    private final T[] buffer;
    private int head, tail, count;

    public BoundedBuffer(int capacity) {
        this.buffer = (T[]) new Object[capacity];
    }

    public void put(T e) throws InterruptedException {
        synchronized (this) {
            while (isFull())
                wait();
            boolean isEmpty = isEmpty();
            doPut(e);
            if (isEmpty) // 条件通知（优化措施）
                notifyAll();
        }
    }

    public T take() throws InterruptedException {
        synchronized (this) {
            while (isEmpty())
                wait();
            boolean isFull = isFull();
            T result = doTake();
            if (isFull) // 条件通知（优化措施）
                notifyAll();
            return result;
        }
    }

    private void doPut(T e) {
        buffer[tail] = e;
        if (++tail == buffer.length)
            tail = 0;
        count++;
    }

    private T doTake() {
        T result = buffer[head];
        buffer[head] = null; // 避免内存泄露
        if (++head == buffer.length)
            head = 0;
        count--;
        return result;
    }

    private boolean isFull() {
        return this.count == buffer.length;
    }

    private boolean isEmpty() {
        return this.count == 0;
    }
}
```

</details>
