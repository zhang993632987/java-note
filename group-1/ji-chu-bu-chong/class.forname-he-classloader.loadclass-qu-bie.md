# Class.forName() 和ClassLoader.loadClass() 区别

* Class.forName()：将类的 .class 文件加载到 jvm 中之外，还会对类进行解释，执行类中的 static 块；
* ClassLoader.loadClass()：只干一件事情，就是将 .class 文件加载到 jvm 中，不会执行 static 中的内容，只有在 newInstance 时才会去执行 static 块。
* Class.forName(name, initialize, loader) 带参函数也可控制是否加载 static 块。
