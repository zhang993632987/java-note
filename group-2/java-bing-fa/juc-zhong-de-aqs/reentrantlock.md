# ReentrantLock

ReentrantLock只支持独占方式的获取操作，因此它实现了**tryAcquire、tryRelease和isHeldExclusively。**

<mark style="color:blue;">**ReentrantLock将同步状态用于保存锁获取操作的次数**</mark>，并且**还维护一个owner变量来保存当前所有者线程的标识符**，只有在当前线程刚刚获取到锁，或者正要释放锁的时候，才会修改这个变量。

```java
  protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            if (compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        else if (current == getExclusiveOwnerThread()) {
            int nextc = c + acquires;
            if (nextc < 0) // overflow
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }

    protected final boolean tryRelease(int releases) {
        int c = getState() - releases;
        if (Thread.currentThread() != getExclusiveOwnerThread())
            throw new IllegalMonitorStateException();
        boolean free = false;
        if (c == 0) {
            free = true;
            setExclusiveOwnerThread(null);
        }
        setState(c);
        return free;
    }

    protected final boolean isHeldExclusively() {
        // While we must in general read state before owner,
        // we don't need to do so to check if current thread is owner
        return getExclusiveOwnerThread() == Thread.currentThread();
    }
```
