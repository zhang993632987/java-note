# CompletionService

如果向 Executor 提交了一组计算任务，并且希望在计算完成后获得结果，可以使用 **CompletionService** 来完成。

**CompletionService 将 Executor 和 BlockingQueue 的功能融合在一起**。可以将 Callable 任务提交给它来执行，然后使用类似于队列操作的 take 和 poll 等方法来获得已完成的结果，而这些结果会在完成时将被封装为 Future。

**ExecutorCompletionService 实现了 CompletionService，并将计算部分委托给一个 Executor。**

<details>

<summary><mark style="color:purple;">ExecutorCompletionService</mark></summary>

```java
public class ExecutorCompletionService<V> implements CompletionService<V> {
    private final Executor executor;
    private final AbstractExecutorService aes;
    private final BlockingQueue<Future<V>> completionQueue;

    public ExecutorCompletionService(Executor executor) {
        if (executor == null)
            throw new NullPointerException();
        this.executor = executor;
        this.aes = (executor instanceof AbstractExecutorService) ?
            (AbstractExecutorService) executor : null;
        this.completionQueue = new LinkedBlockingQueue<Future<V>>();
    }
    
    public Future<V> submit(Callable<V> task) {
        if (task == null) throw new NullPointerException();
        RunnableFuture<V> f = newTaskFor(task);
        executor.execute(new QueueingFuture<V>(f, completionQueue));
        return f;
    }
    
    public Future<V> take() throws InterruptedException {
        return completionQueue.take();
    }

    public Future<V> poll() {
        return completionQueue.poll();
    }

    public Future<V> poll(long timeout, TimeUnit unit)
            throws InterruptedException {
        return completionQueue.poll(timeout, unit);
    }
 
    .....   
} 
```

```java
   /**
     * FutureTask extension to enqueue upon completion.
     */
    private static class QueueingFuture<V> extends FutureTask<Void> {
        private final Future<V> task;
        private final BlockingQueue<Future<V>> completionQueue;
        
        QueueingFuture(RunnableFuture<V> task,
                       BlockingQueue<Future<V>> completionQueue) {
            super(task, null);
            this.task = task;
            this.completionQueue = completionQueue;
        }
        
        protected void done() { 
            completionQueue.add(task); 
        }
    }
```

</details>

<details>

<summary><mark style="color:purple;">FutureTask中run方法调用了done方法</mark></summary>

```java
public class FutureTask<V> implements RunnableFuture<V> {
	
	public void run() {
        ...
        try {
            Callable<V> c = callable;
            if (c != null && state == NEW) {
                V result;
                boolean ran;
                try {
                    result = c.call();
                    ran = true;
                } catch (Throwable ex) {
                    result = null;
                    ran = false;
                    setException(ex);
                }
                if (ran)
                    set(result);
            }
        } finally {
            ...
        }
    }
    
    protected void set(V v) {
        if (STATE.compareAndSet(this, NEW, COMPLETING)) {
            ...
            finishCompletion();
        }
    }
    
    private void finishCompletion() {
        ...

        done();

        callable = null;        // to reduce footprint
    }
    
}
```

</details>

ExecutorCompletionService 的实现非常简单：

* 在构造函数中创建一个 **BlockingQueue** 来**保存计算完成的结果**。
* **当计算完成时，调用 FutureTask 中的 done 方法**。
* 当提交某个任务时，该任务将首先包装为一个 **QueueingFuture**，这是 FutureTask 的一个子类，然后再改写子类的 **done 方法**，并**将结果放入 BlockingQueue 中**。
