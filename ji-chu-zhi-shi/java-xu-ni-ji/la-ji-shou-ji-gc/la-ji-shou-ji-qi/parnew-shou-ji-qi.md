# ParNew 收集器

**ParNew 收集器实质上是 Serial 收集器的多线程并行版本**，除了同时使用多条线程进行垃圾收集之外，其余的行为包括 Serial 收集器可用的所有控制参数、收集算法、Stop The World、对象分配规则、回收策略等都与 Serial 收集器完全一致。

> ParNew 收集器是不少运行在服务端模式下的 HotSpot 虚拟机（尤其是 JDK 7 之前的遗留系统中）首选的新生代收集器，其中有一个与功能、性能无关但其实很重要的原因是：**除了 Serial 收集器外，目前只有它能与 CMS 收集器配合工作。**

**ParNew 收集器是激活 CMS 后（使用 -XX:+UseConcMarkSweepGC 选项）的默认新生代收集器。**
