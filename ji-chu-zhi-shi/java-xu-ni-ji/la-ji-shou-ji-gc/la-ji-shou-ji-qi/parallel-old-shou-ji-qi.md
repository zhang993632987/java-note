# Parallel Old 收集器

**Parallel Old 是 Parallel Scavenge 收集器的老年代版本**，支持**多线程并发收集**，**基于标记-整理算法**实现。

{% hint style="success" %}
**在注重吞吐量或者处理器资源较为稀缺的场合，都可以优先考虑 Parallel Scavenge 加 Parallel Old 收集器这个组合。**
{% endhint %}
