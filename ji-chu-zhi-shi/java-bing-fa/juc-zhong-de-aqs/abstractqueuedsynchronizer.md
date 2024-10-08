# AbstractQueuedSynchronizer

在基于 AQS 构建的同步器类中，最基本的操作包括各种形式的**获取**操作和**释放**操作：

* 获取操作是一种依赖状态的操作，并且通常会阻塞。
* “释放”并不是一个可阻塞的操作，当执行“释放”操作时，所有在请求时被阻塞的线程都会开始执行。

如果一个类想成为状态依赖的类，那么它必须拥有一些状态。<mark style="color:blue;">**AQS 负责管理同步器类中的状态，它管理了一个整数状态信息，可以通过 getState，setState 以及 compareAndSetState 等 protected 方法来进行操作。**</mark>这个整数可以用于表示任意状态。例如，**ReentrantLock 用它来表示所有者线程已经重复获取该锁的次数，Semaphore 用它来表示剩余的许可数量**。

> 在同步器类中还可以自行管理一些额外的状态变量，例如，**ReentrantLock 保存了锁的当前所有者的信息，这样就能区分某个获取操作是重入的还是竞争的。**

下面的程序清单给出了 AQS 中的获取操作与释放操作的形式。

```java
boolean acquire() throws InterruptedException {
    while (当前状态不允许获取操作) {
        if (需要阻塞获取请求) {
            如果当前线程不在队列中，则将其插入队列
            阻塞当前线程
        } else {
            返回失败
        }
    }
    可能更新同步器的状态
    如果线程位于队列中，则将其移出队列
    返回成功
}

void release() {
    更新同步器的状态
    if (新的状态允许某个被阻塞的线程获取成功)
        解除队列中一个或多个线程的阻塞状态
}
```

根据同步器的不同，**获取操作可以是一种独占操作（例如 ReentrantLock），也可以是一个非独占操作（例如 Semaphore 和 CountDownLatch）。**

一个获取操作包括两部分：

1. 首先，同步器判断当前状态是否允许获得操作，如果是，则允许线程执行，否则获取操作将阻塞或失败。这种判断是由同步器的语义决定的。
2. 其次，就是更新同步器的状态，获取同步器的某个线程可能会对其他线程能否也获取该同步器造成影响。

{% hint style="success" %}
<mark style="color:blue;">**如果某个同步器支持独占的获取操作，那么需要实现一些保护方法，包括 tryAcquire、tryRelease 和 isHeldExclusively 等**</mark>，而<mark style="color:blue;">**对于支持共享获取的同步器，则应该实现 tryAcquireShared 和 tryReleaseShared 等方法。**</mark>

AQS 中的 acquire、acquireShared、release 和 releaseShared 等方法都将调用这些方法在子类中带有前缀 try 的版本来判断某个操作是否能执行。

**如果 tryAcquireShared 返回一个负值，那么表示获取操作失败，返回零值表示同步器通过独占方式被获取，返回正值则表示同步器通过非独占方式被获取。对于 tryRelease 和 tryReleaseShared 方法来说，如果释放操作使得所有在获取同步器时被阻塞的线程恢复执行，那么这两个方法应该返回 true。**

在同步器的子类中，可以根据其获取操作和释放操作的语义，使用 getState、setState 以及compareAndSetState 来检查和更新状态，并通过返回的状态值来告知基类“获取”或“释放”同步器的操作是否成功。
{% endhint %}
