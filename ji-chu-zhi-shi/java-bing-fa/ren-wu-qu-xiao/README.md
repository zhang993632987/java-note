# 任务取消

<mark style="color:blue;">**在 Java 中没有一种安全的抢占式方法来停止线程**</mark>，因此也就没有安全的抢占式方法来停止任务。<mark style="color:blue;">**只有一些协作式的机制**</mark>，使请求取消的任务和代码都遵循一种协商好的协议。
