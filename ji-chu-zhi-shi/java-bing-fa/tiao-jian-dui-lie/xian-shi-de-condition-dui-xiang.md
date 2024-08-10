# 显式的 Condition 对象

正如 Lock 是一种广义的内置锁，Condition 也是一种广义的内置条件队列。一个 Condition 和一个 Lock 关联在一起，就像一个条件队列和一个内置锁相关联一样。**要创建一个 Condition，可以在相关联的 Lock 上调用 **<mark style="color:blue;">**Lock.newCondition**</mark>** 方法。**

正如 Lock 比内置加锁提供了更为丰富的功能，Condition 同样比内置条件队列提供了更丰富的功能：在每个锁上可存在多个等待、条件等待可以是可中断的或不可中断的、基于时限的等待，以及公平的或非公平的队列操作。

<mark style="color:blue;">**与内置条件队列不同的是，对于每个 Lock，可以有任意数量的 Condition 对象。**</mark>Condition 对象继承了相关的 Lock 对象的公平性，对于公平的锁，线程会依照 FIFO 顺序从 Condition.await 中释放。

<mark style="color:blue;">**在 Condition 对象中，与 wait、notify 和 notifyAll 方法对应的分别是 await、signal 和 signalAll。**</mark>

<details>

<summary><mark style="color:purple;"><strong>示例：使用显示条件变量的有界缓存</strong></mark></summary>

```java
public class ConditionBoundedBuffer<T> {

    private final T[] buffer;
    private int tail, head, count;

    private final Lock lock = new ReentrantLock();
    private final Condition notFull = lock.newCondition();
    private final Condition notEmpty = lock.newCondition();

    public ConditionBoundedBuffer(int size) {
        this.buffer = (T[]) new Object[size];
    }

    public void put(T e) throws InterruptedException {
        try {
            lock.lock();
            while (isFull())
                notFull.await();
            putInternal(e);
            notEmpty.signal();
        } finally {
            lock.unlock();
        }
    }

    public T take() throws InterruptedException {
        try {
            lock.lock();
            while (isEmpty())
                notEmpty.await();
            T result = takeInternal();
            notFull.signal();
            return result;
        } finally {
            lock.unlock();
        }
    }

    private void putInternal(T e) {
        buffer[tail] = e;
        count++;
        tail = (tail + 1) % buffer.length;
    }

    private T takeInternal() {
        T result = buffer[head];
        buffer[head] = null;
        count--;
        head = (head + 1) % buffer.length;
        return result;
    }

    private boolean isFull() {
        return count >= buffer.length;
    }

    private boolean isEmpty() {
        return count <= 0;
    }
}
```

</details>
