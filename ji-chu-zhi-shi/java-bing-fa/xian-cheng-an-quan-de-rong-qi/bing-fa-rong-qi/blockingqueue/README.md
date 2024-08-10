# BlockingQueue

**阻塞队列**提供了**可阻塞的 put 和 take 方法**，以及**支持定时的 offer 和 poll 方法**。

* 如果队列已经满了，那么 put 方法将阻塞直到有空间可用；如果队列为空，那么 take 方法将会阻塞直到有元素可用。
* 队列可以是有界的也可以是无界的，无界队列永远都不会充满，因此无界队列上的 put 方法也永远不会阻塞。

在类库中包含了 BlockingQueue 的多种实现，其中：

* <mark style="color:blue;">**LinkedBlockingQueue**</mark> 和 <mark style="color:blue;">**ArrayBlockingQueue**</mark> 是 FIFO 队列，二者分别与 LinkedList 和 ArrayList 类似，但比同步 List 拥有更好的并发性能。
* <mark style="color:blue;">**PriorityBlockingQueue**</mark> 是一个按优先级排序的队列，当你希望按照某种顺序而不是 FIFO 来处理元素时，这个队列将非常有用。正如其他有序的容器一样，**PriorityBlockingQueue 既可以根据元素的自然顺序来比较元素（如果它们实现了 Comparable 方法），也可以使用 Comparator 来比较。**
*   最后一个 BlockingQueue 实现是 <mark style="color:blue;">**SynchronousQueue**</mark>，实际上它不是一个真正的队列，因为它不会为队列中元素维护存储空间。**与其他队列不同的是，它维护一组线程，这些线程在等待着把元素加入或移出队列。因为 SynchronousQueue 没有存储功能，因此 put 和 take 会一直阻塞，直到有另一个线程已经准备好参与到交付过程中。**<mark style="color:orange;">**仅当有足够多的消费者，并且总是有一个消费者准备好获取交付的工作时，才适合使用 SynchronousQueue。**</mark>



    {% hint style="success" %}
    SynchronousQueue 的实现方式看似很奇怪，但**由于可以直接交付工作，从而降低了将数据从生产者移动到消费者的延迟。直接交付方式还会将更多关于任务状态的信息反馈给生产者。**当交付被接受时，它就知道消费者已经得到了任务，而不是简单地把任务放入一个队列——这种区别就好比将文件直接交给同事，还是将文件放到她的邮箱中并希望她能尽快拿到文件。
    {% endhint %}
