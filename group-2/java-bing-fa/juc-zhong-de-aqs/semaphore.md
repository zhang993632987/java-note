# Semaphore

<mark style="color:blue;">**Semaphore将AQS的同步状态用于保存当前可用许可的数量。**</mark>

```java
protected int tryAcquireShared(int acquires) {
    for (;;) {
        int available = getState();
        int remaining = available - acquires;
        if (remaining < 0 ||
            compareAndSetState(available, remaining))
            return remaining;
    }
}

protected final boolean tryReleaseShared(int releases) {
    for (;;) {
        int current = getState();
        int next = current + releases;
        if (next < current) // overflow
            throw new Error("Maximum permit count exceeded");
        if (compareAndSetState(current, next))
            return true;
    }
}
```



tryAcquireShared方法首先计算剩余许可的数量，如果没有足够的许可，那么会返回一个值表示获取操作失败。**如果还有剩余的许可，那么tryAcquireShared会通过compareAndSetState以原子方式来降低许可的计数。**如果这个操作成功，那么将返回一个值表示获取操作成功。

tryReleaseShared将增加许可计数，这可能会解除等待中线程的阻塞状态，并且不断地重试直到更新操作成功。**tryReleaseShared的返回值表示在这次释放操作中解除了其他线程的阻塞。**
