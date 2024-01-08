# Executors

<mark style="color:blue;">**Executors**</mark>类提供了一些**静态工厂方法**用于创建线程池。

## newFixedThreadPool

newFixedThreadPool将创建一个**固定长度的线程池**，每当提交一个任务时就创建一个线程，直到达到线程池的最大数量，这时线程池的规模将不再变化（如果某个线程由于发生了未预期的Exception而结束，那么线程池会补充一个新的线程）。

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}
```

## newCachedThreadPool

newCachedThreadPool将创建一个可缓存的线程池，如果线程池的当前规模超过了处理需求时，那么将回收空闲的线程，而当需求增加时，则可以添加新的线程，**线程池的规模不存在任何限制**。

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

## newSingleThreadExecutor

newSingleThreadExecutor是一个**单线程**的Executor，它创建单个工作者线程来执行任务，如果这个线程异常结束，会创建另一个线程来替代。

```java
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
```

## newScheduledThreadPool

newScheduledThreadPool创建了一个**固定长度的线程池**，而且**以延迟或定时的方式来执行任务。**

```java
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
    return new ScheduledThreadPoolExecutor(corePoolSize);
}
```

## unconfigurableExecutorService

在Executors中包含一个**unconfigurableExecutorService**工厂方法，**该方法对一个现有的ExecutorService进行包装，使其只暴露出ExecutorService的方法**，因此不能修改它的配置信息。

<mark style="color:blue;">**如果将ExecutorService暴露给不信任的代码，又不希望对其进行修改，就可以通过unconfigurableExecutorService来包装它。**</mark>

> newSingleThreadExecutor返回按这种方式封装的ExecutorService。这样可以防止用户增加单线程 Executor 的线程池大小，从而破坏它的执行语义。
