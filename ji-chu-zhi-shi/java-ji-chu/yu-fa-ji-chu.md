# 语法基础

## 多态 <a href="#jian-shu-java-de-duo-tai" id="jian-shu-java-de-duo-tai"></a>

<mark style="color:blue;">**多态主要指继承父类和实现接口时，可使用父类引用指向子类对象。**</mark>

{% hint style="success" %}
运行时多态的实现主要依靠<mark style="color:blue;">**方法表**</mark>，方法表中最先存放的是 Object 类的方法，接下来是该类的父类的方法，最后是该类本身的方法。

**如果子类改写了父类的方法，那么子类和父类的那些同名方法共享一个方法表项，都被认作是父类的方法**。因此可以实现运行时多态。
{% endhint %}

## 重载与重写 <a href="#zhong-zai-yu-fu-gai-de-qu-bie" id="zhong-zai-yu-fu-gai-de-qu-bie"></a>

* 重写是父类与子类之间的关系，是**垂直关系**；重载是同一类中方法之间的关系，是**水平关系**。
* 重写要求**方法签名**相同；重载要求方法签名不同。

{% hint style="success" %}
方法重写的限制条件：

* [x] 重写要求方法签名相同。
* [x] **返回的类型必须与被重写的方法的返回类型相同，或者是被重写的方法的返回类型的子类型**。
* [x] 访问**修饰符的限制一定要大于被重写方法的访问修饰符**。
* [x] 重写方法一定**不能抛出新的检查异常或者比被重写方法申明更加宽泛的检查型异常**。

```java
public class Test {

    public static class SuperClass {
        public Object test() {
            return "";
        }
    }

    public static class ChildClass extends SuperClass {
        @Override
        public String test() {
            return "child";
        }
    }

    public static void main(String[] args) {
        // 子类方法的返回类型与父类方法的返回类型不同
        SuperClass obj = new ChildClass();
        System.out.println(obj.test());
    }
}
```
{% endhint %}

## static 关键字 <a href="#java-yu-yan-zhong-guan-jian-zi-static-de-zuo-yong-shi-shen-me" id="java-yu-yan-zhong-guan-jian-zi-static-de-zuo-yong-shi-shen-me"></a>

* <mark style="color:blue;">**修饰变量**</mark>
  * 用 static 关键字修饰的**静态变量**在内存中只有一个副本。
  * **只要静态变量所在的类被加载，这个静态变量就会被分配空间。**
  * 可以使用“类.静态变量”和“对象.静态变量”的方法使用。
* <mark style="color:blue;">**修饰方法**</mark>
  * static 修饰的**静态方法**无需创建对象就可以被调用。
  * static 方法中不能使用 this 和 super 关键字，不能调用非 static 方法，只能访问所属类的静态成员变量和静态成员方法。
* <mark style="color:blue;">**修饰代码块**</mark>
  * JVM 在加载类的时候会执行 **static 代码块**。
  * static 代码块常用于初始化静态变量。
  * static 代码块只会被执行一次。
* <mark style="color:blue;">**修饰内部类**</mark>
  * **static 内部类**可以不依赖外部类的实例对象而被实例化。
  * 静态内部类不能与外部类有相同的名字，不能访问普通成员变量，只能访问外部类中的静态成员和静态成员方法。

## final、finally 和 finalize <a href="#finalfinally-he-finalize-de-qu-bie-shi-shen-me" id="finalfinally-he-finalize-de-qu-bie-shi-shen-me"></a>

* final 是一个修饰符，可以修饰变量、方法和类，分别表示**属性不可变**、**方法不可覆盖**、**类不可继承**。
* finally 作为异常处理的一部分，只能在 try/catch 语句中使用，finally 附带一个语句块用来表示这个语句最终一定被执行，经常被用在需要释放资源的情况下。
* finalize 是 Object 类的一个方法，在垃圾收集器执行的时候会调用被回收对象的 finalize() 方法。当垃圾回收器准备好释放对象占用的内存空间时，首先会调用 finalize() 方法，并在下一次垃圾回收动作发生时真正回收对象占用的内存。

## 判等运算符 == 与 equals 的区别？ <a href="#pan-deng-yun-suan-fu-yu-equals-de-qu-bie" id="pan-deng-yun-suan-fu-yu-equals-de-qu-bie"></a>

* \== 比较的是引用，equals 比较的是内容。
* 如果变量是基础数据类型，== 用于比较其对应值是否相等。如果变量指向的是对象，== 用于比较两个对象是否指向同一块存储空间。
* equals 是 Object 类提供的方法之一，每个 Java 类都继承自 Object 类，所以每个对象都具有 equals 方法。Object 类中定义的 equals 方法在内部直接调用 == 进行比较，而通过重写 equals 方法可以让它不是比较引用而是比较对象内容。

## Java 的基本数据类型

* **整型**：byte、short、int、long
* **浮点型**：float、double
* **字符型**：char
* **布尔型**：boolean

## 自动装箱与拆箱 <a href="#jian-shu-zi-dong-zhuang-xiang-chai-xiang" id="jian-shu-zi-dong-zhuang-xiang-chai-xiang"></a>

对于 Java 基本数据类型，均对应一个包装器类。

* 装箱就是自动将基本数据类型转换为包装器类型，如 int -> Integer
* 拆箱就是自动将包装器类型转换为基本数据类型，如 Integer -> int

## String、StringBuffer 与 StringBuilder <a href="#jian-shu-stringstringbuffer-yu-stringbuilder" id="jian-shu-stringstringbuffer-yu-stringbuilder"></a>

* **一个 String 对象在 Java 中表示一个字符串常量，是一个不可变对象，任何修改 String 的操作都不会改变原有的字符串，而是会生成一个新的字符串对象。**
* **StringBuffer 和 StringBuilder 均是工具类，主要用于构造字符串，减少频繁修改字符串导致创建过多的字符串对象**。两者的区别主要在于 StringBuffer 具有线程安全性，而 StringBuilder 并不具有线程安全性。

## Object 类常用方法 <a href="#jian-shu-object-lei-chang-yong-fang-fa" id="jian-shu-object-lei-chang-yong-fang-fa"></a>

* hashCode：返回对象的散列码，一般当对象作为 HashSet 中的元素或 HashMap 中的键时，需要重写该方法。
* equals：判断两个对象是否相等，需保证 equals 结果为 true 的两个对象的 hashCode 值也一样。
* toString：返回对象的字符串表示。
* **clone：克隆一个对象，需要实现 Clonable 接口该方法才能使用。**
* wait：当调用该方法时，当前线程会被放入对象的条件队列中进行等待，并释放对象锁。
* notify / notfiyAll：当调用该方法时，会唤醒在对象条件队列中等待的线程。其中 notify 会唤醒其中某一个线程，而 notifyAll 则会唤醒所有线程。
