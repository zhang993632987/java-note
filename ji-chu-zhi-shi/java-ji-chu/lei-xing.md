# 类型

## 接口和抽象类 <a href="#jie-kou-he-chou-xiang-lei-de-xiang-tong-dian-he-bu-tong-dian" id="jie-kou-he-chou-xiang-lei-de-xiang-tong-dian-he-bu-tong-dian"></a>

**相同点:**

* 都不能被实例化。
* 接口的实现类或抽象类的子类需实现接口或抽象类中的抽象方法才能被实例化。

**不同点：**

* 一个子类只能继承一个抽象类，但能实现多个接口
* 抽象类可以有构造方法，接口没有构造方法
* 成员变量：
  * 抽象类可以有普通成员变量，接口没有普通成员变量
  * 抽象类和接口都可有静态成员变量，但接口只能public static final(默认)，而抽象类则没有此限制
* 方法：
  * 抽象类可以没有抽象方法，抽象类可以有普通方法；接口在JDK8之前都是抽象方法，在JDK8可以有default方法，在JDK9中允许有私有普通方法
  * 抽象类可以有静态方法；接口在JDK8之前不能有静态方法，在JDK8中可以有静态方法，且只能被接口类直接调用（不能被实现类的对象调用）
  * 抽象类中的方法可以是public、protected；接口方法在JDK8之前只有public abstract，在JDK8可以有default方法，在JDK9中允许有private方法

## 内部类 <a href="#jian-shu-nei-bu-lei-ji-qi-zuo-yong" id="jian-shu-nei-bu-lei-ji-qi-zuo-yong"></a>

内部类根据不同的定义方式，可分为静态内部类、成员内部类、局部内部类和匿名内部类这4种。

* <mark style="color:blue;">**静态内部类**</mark>：定义在类内部的静态类被称为静态内部类。
  * 静态内部类可以访问外部类的静态变量和方法。
  * 在静态内部类中可以定义静态变量、方法、构造函数等。
  * 静态内部类通过“外部类.静态内部类”的方式来调用。
* <mark style="color:blue;">**成员内部类**</mark>：定义在类内部的非静态类叫作成员内部类。
  * 成员内部类不能定义静态方法和变量（final修饰的除外）。
  * **可以访问外部类的私有属性和方法。**
  * 外部类想要访问内部类属性或方法时，必须要创建一个内部类对象，然后通过该对象访问内部类的属性或方法。
  * 外部类也可访问 private 修饰的内部类属性。
* <mark style="color:blue;">**局部内部类**</mark>：存在于方法中的内部类。访问权限类似局部变量，只能访问外部类的 final 变量。
* <mark style="color:blue;">**匿名内部类**</mark>：只能使用一次，没有类名，只能访问外部类的 final 变量。
