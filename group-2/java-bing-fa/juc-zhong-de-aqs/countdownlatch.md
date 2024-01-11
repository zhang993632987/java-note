# CountDownLatch

CountDownLatch使用AQS的方式与Semaphore很相似：<mark style="color:blue;">**在同步状态中保存的是当前的计数值。**</mark>

* countDown方法调用release，从而导致计数值递减，并且当计数值为零时，解除所有等待线程的阻塞。
* await调用acquire，当计数器为零时，acquire将立即返回，否则将阻塞。

```java
protected int tryAcquireShared(int acquires) {
    return (getState() == 0) ? 1 : -1;
}

protected boolean tryReleaseShared(int releases) {
    // Decrement count; signal when transition to zero
    for (;;) {
        int c = getState();
        if (c == 0)
            return false;
        int nextc = c-1;
        if (compareAndSetState(c, nextc))
            return nextc == 0;
    }
}
```
