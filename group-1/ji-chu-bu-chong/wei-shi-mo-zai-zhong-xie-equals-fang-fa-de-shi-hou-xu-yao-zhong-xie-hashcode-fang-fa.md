# 为什么在重写 equals 方法的时候需要重写 hashCode 方法

这是为了满足“逻辑上相等（equals）的对象，它们的 hash code 也需要相等”这个规范，只有满足这个规范才能在 HashSet、HashMap 这些数据结构上正确使用该对象。

例如：

<pre class="language-java"><code class="lang-java">Map&#x3C;PhoneNumber, String> m = new HashMap&#x3C;>();
<strong>m.put(new PhoneNumber(707, 867, 5309), "Jenny");
</strong></code></pre>

PhoneNumber 重写了 equals 方法，但没有重写 hashCode 方法。当使用下述方式查询 HashMap 时，其结果为 null，而不是 Jenny：

```java
m.get(new PhoneNumber(707, 867, 5309))
```

这是因为在调用 get 方法时，虽然**传入的是一个与 put 方法传入的 key 对象逻辑上相等的对象**，但因为并未实现 hashCode 方法，这两个对象的 hash code 并不相等，因此无法找到目标 entry。
