# Java 基础八股文

## Java 语言具有哪些特点？ <a href="#java-yu-yan-ju-you-na-xie-te-dian" id="java-yu-yan-ju-you-na-xie-te-dian"></a>

* Java 为纯**面向对象**的语言。
* 具有**平台无关性**。编译器把 Java 代码编译成平台无关的中间代码，然后在 JVM 上运行
* Java 程序是通过解释器进行**解释执行**的，但部分商用虚拟机为了提高热点代码的执行效率，会利用**即时编译**器将代码编译成与本地平台无法的机器码，并进行各种优化。
* Java 提供了很多**内置类库**。如对多线程支持，对网络通信支持，最重要的一点是提供了垃圾回收器。
* Java 具有较好的**安全性**和**健壮性**。Java 提供了异常处理和垃圾回收机制，去除了 C++中难以理解的指针特性。

## JDK 与 JRE  <a href="#jdk-yu-jre-you-shen-me-qu-bie" id="jdk-yu-jre-you-shen-me-qu-bie"></a>

* JDK：Java 开发工具包（Java Development Kit），提供了 Java 的开发环境和运行环境。
* JRE：Java 运行环境(Java Runtime Environment)，提供了 Java 运行所需的环境。
* JDK 包含了 JRE。如果只运行 Java 程序，安装 JRE 即可。要编写 Java 程序需安装 JDK。

## 简述Java 基本数据类型

* 整型：byte、short、int、long
* 浮点型：float、double
* 字符型：char
* 布尔型：boolean

## 简述自动装箱拆箱 <a href="#jian-shu-zi-dong-zhuang-xiang-chai-xiang" id="jian-shu-zi-dong-zhuang-xiang-chai-xiang"></a>

对于 Java 基本数据类型，均对应一个包装器类。

装箱就是自动将基本数据类型转换为包装器类型，如 int->Integer

拆箱就是自动将包装器类型转换为基本数据类型，如 Integer->int

## 简述 Java 访问修饰符 <a href="#jian-shu-java-fang-wen-xiu-shi-fu" id="jian-shu-java-fang-wen-xiu-shi-fu"></a>

* private: 在同一类内可见，不能修饰类
* default: 默认访问修饰符，在同一包内可见
* protected : 对同一包内的类和所有子类可见，不能修饰类
* public: 对所有类可见

## 构造方法、成员变量初始化以及静态变量三者的初始化顺序？ <a href="#gou-zao-fang-fa-cheng-yuan-bian-liang-chu-shi-hua-yi-ji-jing-tai-cheng-yuan-bian-liang-san-zhe-de-ch" id="gou-zao-fang-fa-cheng-yuan-bian-liang-chu-shi-hua-yi-ji-jing-tai-cheng-yuan-bian-liang-san-zhe-de-ch"></a>

先后顺序：静态变量、成员变量、构造方法。

详细的先后顺序：父类静态变量、父类静态代码块、子类静态变量、子类静态代码块、父类非静态变量、父类非静态代码块、父类构造函数、子类非静态变量、子类非静态代码块、子类构造函数。

## Java 代码块执行顺序 <a href="#java-dai-ma-kuai-zhi-hang-shun-xu" id="java-dai-ma-kuai-zhi-hang-shun-xu"></a>

1. 父类静态代码块（只执行一次）
2. 子类静态代码块（只执行一次）
3. 父类构造代码块
4. 父类构造函数
5. 子类构造代码块
6. 子类构造函数
7. 普通代码块

## 面向对象的三大特性？ <a href="#mian-xiang-dui-xiang-de-san-da-te-xing" id="mian-xiang-dui-xiang-de-san-da-te-xing"></a>

* **封装**：将客观事物抽象成类，每个类可以把自身数据和方法只让可信的类或对象操作，对不可信的进行信息隐藏。
* **继承**：对象的一个新类可以从现有的类中派生，派生类可以从它的基类那继承方法和实例变量，且派生类可以修改或新增新的方法使之更适合特殊的需求。
* **多态**：允许不同类的对象对同一消息作出响应。不同对象调用相同方法即使参数也相同，最终表现行为是不一样的。

## 为什么 Java 语言不支持多重继承？ <a href="#wei-shen-me-java-yu-yan-bu-zhi-chi-duo-zhong-ji-cheng" id="wei-shen-me-java-yu-yan-bu-zhi-chi-duo-zhong-ji-cheng"></a>

* 为了程序的结构能够更加清晰从而便于维护。假设 Java 语言支持多重继承，类 C 继承自类 A 和类 B，如果类 A 和 B 都有自定义的成员方法 `f()`，那么当代码中调用类 C 的 `f()` 会产生二义性。
* Java 语言通过实现多个接口间接支持多重继承，接口由于只包含方法定义，不能有方法的实现，类 C 继承接口 A 与接口 B 时即使它们都有方法`f()`，也不能直接调用方法，需实现具体的`f()`方法才能调用，不会产生二义性。
* 多重继承会使类型转换、构造方法的调用顺序变得复杂，会影响到性能。

## 简述 Java 的多态 <a href="#jian-shu-java-de-duo-tai" id="jian-shu-java-de-duo-tai"></a>

多态主要指继承父类和实现接口时，可使用**父类引用指向子类对象**。

运行时多态的实现：主要依靠方法表，方法表中最先存放的是 Object 类的方法，接下来是该类的父类的方法，最后是该类本身的方法。如果子类改写了父类的方法，那么子类和父类的那些同名方法共享一个方法表项，都被认作是父类的方法。因此可以实现运行时多态。

## 重载与重写的区别？ <a href="#zhong-zai-yu-fu-gai-de-qu-bie" id="zhong-zai-yu-fu-gai-de-qu-bie"></a>

* 重写是父类与子类之间的关系，是**垂直关系**；重载是同一类中方法之间的关系，是**水平关系**。
* 重写要求**方法签名**相同；重载要求方法签名不同。

> 重写：
>
> 1. 重写要求方法签名相同。
> 2. **返回的类型必须与被重写的方法的返回类型相同，或者是被重写的方法的返回类型的子类型**。
> 3. 访问**修饰符的限制一定要大于被重写方法的访问修饰符** 。
> 4. 重写方法一定**不能抛出新的检查异常或者比被重写方法申明更加宽泛的检查型异常**。
>
> ```java
> public class Test {
>
>     public static class SuperClass {
>         public Object test() {
>             return "";
>         }
>     }
>
>     public static class ChildClass extends SuperClass {
>         @Override
>         public String test() {
>             return "child";
>         }
>     }
>
>     public static void main(String[] args) {
>         // 子类方法的返回类型与父类方法的返回类型不同
>         SuperClass obj = new ChildClass();
>         System.out.println(obj.test());
>     }
> }
> ```

## 接口和抽象类的相同点和不同点？ <a href="#jie-kou-he-chou-xiang-lei-de-xiang-tong-dian-he-bu-tong-dian" id="jie-kou-he-chou-xiang-lei-de-xiang-tong-dian-he-bu-tong-dian"></a>

相同点:

* 都不能被实例化。
* 接口的实现类或抽象类的子类需实现接口或抽象类中的抽象方法才能被实例化。

不同点：

* 接口只能有方法定义，不能有方法的实现，而抽象类可以有方法的定义与实现。
* 关键字不同，接口通过interface关键字进行定义；而抽象类通过abstract class关键字进行定义。
* 一个类可以实现多个接口，但最多只能继承一个抽象类。
* 接口中定义的所有方法的作用域均为public，而抽象类中方法可以使用四种访问修饰符中的任意一种进行修饰。
* 接口中不能定义实例字段，只能定义静态常量，因为接口中的字段会默认添加public static final进行修饰，而抽象类无此限制。

## 简述内部类及其作用 <a href="#jian-shu-nei-bu-lei-ji-qi-zuo-yong" id="jian-shu-nei-bu-lei-ji-qi-zuo-yong"></a>

* **成员内部类**：作为成员对象的内部类。可以访问 private 及以上外部类的属性和方法。外部类想要访问内部类属性或方法时，必须要创建一个内部类对象，然后通过该对象访问内部类的属性或方法。外部类也可访问 private 修饰的内部类属性。
* **局部内部类**：存在于方法中的内部类。访问权限类似局部变量，只能访问外部类的 final 变量。
* **匿名内部类**：只能使用一次，没有类名，只能访问外部类的 final 变量。
* **静态内部类**：类似类的静态成员变量。

## Java 语言中关键字 static 的作用是什么？ <a href="#java-yu-yan-zhong-guan-jian-zi-static-de-zuo-yong-shi-shen-me" id="java-yu-yan-zhong-guan-jian-zi-static-de-zuo-yong-shi-shen-me"></a>

* 修饰变量。用 static 关键字修饰的**静态变量**在内存中只有一个副本。只要静态变量所在的类被加载，这个静态变量就会被分配空间，可以使用“类.静态变量”和“对象.静态变量”的方法使用。
* 修饰成员方法。static 修饰的**静态方法**无需创建对象就可以被调用。static 方法中不能使用 this 和 super 关键字，不能调用非 static 方法，只能访问所属类的静态成员变量和静态成员方法。
* 修饰代码块。JVM 在加载类的时候会执行 **static 代码块**。static 代码块常用于初始化静态变量。static 代码块只会被执行一次。
* 修饰内部类。**static 内部类**可以不依赖外部类实例对象而被实例化。静态内部类不能与外部类有相同的名字，不能访问普通成员变量，只能访问外部类中的静态成员和静态成员方法。

## 为什么String要设计为不可变的？ <a href="#wei-shen-me-yao-ba-string-she-ji-wei-bu-ke-bian" id="wei-shen-me-yao-ba-string-she-ji-wei-bu-ke-bian"></a>

* **节省空间**：字符串常量存储在 JVM 的字符串常量池，从而被多个变量所共享。
* **线程安全**：不可变对象天然便具有线程安全性，在涉及多线程的操作中不需要同步操作。

## 简述 String/StringBuffer 与 StringBuilder <a href="#jian-shu-stringstringbuffer-yu-stringbuilder" id="jian-shu-stringstringbuffer-yu-stringbuilder"></a>

* 一个String对象在Java中表示一个字符串常量，是一个不可变对象，任何修改String的操作都不会改变原有的字符串，而是会生成一个新的字符串对象。
* StringBuffer 和 StringBuilder 均是工具类，主要用于构造字符串，减少频繁修改字符串导致创建过多的字符串对象。两者的区别主要在于StringBuffer具有线程安全性，而StringBuilder并不具有线程安全性。

## 判等运算符==与 equals 的区别？ <a href="#pan-deng-yun-suan-fu-yu-equals-de-qu-bie" id="pan-deng-yun-suan-fu-yu-equals-de-qu-bie"></a>

\== 比较的是引用，equals 比较的是内容。

如果变量是基础数据类型，== 用于比较其对应值是否相等。如果变量指向的是对象，== 用于比较两个对象是否指向同一块存储空间。

equals 是 Object 类提供的方法之一，每个 Java 类都继承自 Object 类，所以每个对象都具有 equals 这个方法。Object 类中定义的 equals 方法在内部直接调用 == 进行比较，而通过重写equals方法可以让它不是比较引用而是比较对象内容。

## 简述 Object 类常用方法 <a href="#jian-shu-object-lei-chang-yong-fang-fa" id="jian-shu-object-lei-chang-yong-fang-fa"></a>

* hashCode：返回对象的散列码hashCode，一般当对象作为HashSet中的元素或HashMap中的键时，需要重写该方法。
* equals：判断两个对象是否相等，需保证equals结果为true的两个对象的hashCode值也一样。
* toString：返回对象的字符串表示。
* clone：克隆一个对象，需要实现Clonable接口该方法才能使用。
* wait：当调用该方法时，当前线程会被放入对象的条件队列中进行等待，并释放对象锁。
* notify / notfiyAll：当调用该方法时，会唤醒在对象条件队列中等待的线程。其中notify会唤醒其中某一个线程，而notifyAll则会唤醒所有线程。

## Java 中一维数组和二维数组的声明方式？ <a href="#java-zhong-yi-wei-shu-zu-he-er-wei-shu-zu-de-sheng-ming-fang-shi" id="java-zhong-yi-wei-shu-zu-he-er-wei-shu-zu-de-sheng-ming-fang-shi"></a>

* 一维数组：Type array\[] 或 Type\[] array
* 二维数组：Type array\[]\[] 、Type\[]\[] array 或 Type\[] array\[]

## 简述 Java 异常的分类 <a href="#jian-shu-java-yi-chang-de-fen-lei" id="jian-shu-java-yi-chang-de-fen-lei"></a>

Java 中所有的异常 Exception 和错误 Error 有一个共同的祖先 Throwable，其中：

* Error 表示虚拟机内部错误，例如 OutOfMemoryError、StackOverflowError
* Exception 又分为两类：
  * 非检查型异常：所有RuntimeException的子类均是非检查异常，对于非检查异常，并不强制要求必须进行处理。例如：NullPointerException、NumberFormatException、IllegalArgumentException。
  * 检查型异常：在所有Exception的子类中，排除掉RuntimeException及其子类，剩下的所有异常均为检查型异常。与非检查型异常不同，检查型异常必须被得到处理，否则编译器会报错。例如：InterruptedException、IOException、FileNotFoundException。

## 简述 throw 与 throws 的区别 <a href="#jian-shu-throw-yu-throws-de-qu-bie" id="jian-shu-throw-yu-throws-de-qu-bie"></a>

throw 一般是用在方法体的内部，由开发者定义当程序语句出现问题后主动抛出一个异常。

throws 一般用于方法声明上，代表该方法可能会抛出的异常列表。

## 出现在 Java 程序中的 finally 代码块是否一定会执行？ <a href="#chu-xian-zai-java-cheng-xu-zhong-de-finally-dai-ma-kuai-shi-fou-yi-ding-hui-zhi-hang" id="chu-xian-zai-java-cheng-xu-zhong-de-finally-dai-ma-kuai-shi-fou-yi-ding-hui-zhi-hang"></a>

当遇到下面情况不会执行。

* 当程序在进入 try 语句块之前就出现异常时会直接结束。
* 当程序在 try 块中强制退出时，如使用 System.exit(0)，也不会执行 finally 块中的代码。

其它情况下，在 try/catch/finally 语句执行的时候，try 块先执行，当有异常发生，执行 catch 代码块，无论是否发生异常，都会执行 finally 代码块。

值得注意的是，当 try/catch 语句块中有 return 时，finally 语句块中的代码会在 return 之前执行。如果 try/catch/finally 块中都有 return 语句，finally 块中的 return 语句会覆盖 try/catch 模块中的 return 语句。

## final、finally 和 finalize 的区别是什么？ <a href="#finalfinally-he-finalize-de-qu-bie-shi-shen-me" id="finalfinally-he-finalize-de-qu-bie-shi-shen-me"></a>

* final 用于声明属性、方法和类，分别表示**属性不可变**、**方法不可覆盖**、**类不可继承**。
* finally 作为异常处理的一部分，只能在 try/catch 语句中使用，finally 附带一个语句块用来表示这个语句最终一定被执行，经常被用在需要释放资源的情况下。
* finalize 是 Object 类的一个方法，在垃圾收集器执行的时候会调用被回收对象的 finalize()方法。当垃圾回收器准备好释放对象占用的内存空间时，首先会调用 finalize()方法，并在下一次垃圾回收动作发生时真正回收对象占用的内存。

## 简述泛型 <a href="#jian-shu-fan-xing" id="jian-shu-fan-xing"></a>
