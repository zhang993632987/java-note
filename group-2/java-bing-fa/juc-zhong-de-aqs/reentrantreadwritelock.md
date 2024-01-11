# ReentrantReadWriteLock

ReadWriteLock接口表示存在两个锁：一个读取锁和一个写入锁。<mark style="color:blue;">**在基于AQS实现的ReentrantReadWriteLock中，单个AQS子类将同时管理读取加锁和写入加锁。**</mark>

**ReentrantReadWriteLock使用了一个16位的状态来表示写入锁的计数**，并且**使用了另一个16位的状态来表示读取锁的计数**。**在读取锁上的操作将使用共享的获取方法与释放方法，在写入锁上的操作将使用独占的获取方法与释放方法。**

**AQS在内部维护一个等待线程队列，其中记录了某个线程请求的是独占访问还是共享访问。**在ReentrantReadWriteLock中，当锁可用时，如果位于队列头部的线程执行写入操作，那么线程会得到这个锁，如果位于队列头部的线程执行读取访问，那么队列中在第一个写入线程之前的所有线程都将获得这个锁。

<details>

<summary><mark style="color:purple;">ReadLock</mark></summary>

```java
public static class ReadLock implements Lock, java.io.Serializable {

    private final Sync sync;

    protected ReadLock(ReentrantReadWriteLock lock) {
        sync = lock.sync;
    }

    public void lock() {
        sync.acquireShared(1);
    }
    
    public void unlock() {
        sync.releaseShared(1);
    }
}
```

</details>

<details>

<summary><mark style="color:purple;">WriteLock</mark></summary>

```java
public static class WriteLock implements Lock, java.io.Serializable {

    private final Sync sync;

    protected WriteLock(ReentrantReadWriteLock lock) {
        sync = lock.sync;
    }

    public void lock() {
        sync.acquire(1);
    }
    
    public void unlock() {
        sync.release(1);
    }
}
```

</details>
