# 非阻塞的栈

> <mark style="color:blue;">**创建非阻塞算法的关键在于，如何将原子修改的范围缩小到单个变量上，同时还要维护数据的一致性。**</mark>

<details>

<summary><mark style="color:purple;">ConcurrentStack</mark></summary>

```java
public class ConcurrentStack<E> {

    private AtomicReference<Node<E>> top = new AtomicReference<>();

    public void push(E e) {
        Node<E> newNode = new Node<>(e);
        Node<E> oldNode;
        do {
            oldNode = top.get();
            newNode.next = oldNode;
        } while (!top.compareAndSet(oldNode, newNode));
    }

    public E pop() {
        while (true) {
            Node<E> oldTop = top.get();
            if (oldTop != null) {
                if (top.compareAndSet(oldTop, oldTop.next)) {
                    oldTop.next = null;
                    return oldTop.val;
                }
            } else {
                return null;
            }
        }
    }
}
```

</details>

上面的栈是由 Node 元素构成的一个链表，其中栈顶作为根节点，并且在每个元素中都包含了一个值以及指向下一个元素的链接。

push 方法创建一个新的节点，该节点的 next 域指向当前的栈顶，然后使用 CAS 把这个新节点放入栈顶。

* 如果在开始插入节点时，位于栈顶的节点没有发生变化，那么CAS就会成功
* 如果栈顶节点发生了变化（例如由于其他线程在本线程开始之前插入或移除了元素），那么CAS将会失败，而push方法会根据栈的当前状态来更新节点，并且再次尝试。

无论哪种情况，在CAS执行完成后，栈仍会处于一致的状态。
