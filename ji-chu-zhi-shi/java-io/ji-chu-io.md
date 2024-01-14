# 基础IO

在整个**Java.io包**中最重要的是 5 个类和 1 个接口：

* 5 个类指的是 **File**、**OutputStream**、**InputStream**、**Writer**、**Reader**
* 1 个接口指的是 **Serializable**

## **从数据传输方式**的角度

从数据传输方式的角度看，可以将 IO 类分为：

<details>

<summary><mark style="color:purple;"><strong>字节流</strong></mark></summary>

* **InputStream**
  * **ByteArrayInputStream**
  * **FileInputStream**
  * **PipedInputStream**
  * **FilterInputStream**
    * **DataInputStream**
    * **BufferedInputStream**
  * **ObjectInputStream**

<!---->

* **OutputStream**
  * **ByteArrayOutputStream**
  * **FileOutputStream**
  * **PipedOutputStream**
  * **FilterOutputStream**
    * **DataOutputStream**
    * **BufferedOutputStream**
    * **PrintStream**
  * **ObjectOutputStream**

</details>

<details>

<summary><mark style="color:purple;"><strong>字符流</strong></mark></summary>

* **Reader**
  * **CharArrayReader**
  * **InputStreamReader**
    * **FileReader**
  * **PipedReader**
  * **FilterReader**
  * **BufferedReader**
    * **LineNumberReader**
  * **StringReader**
  * **LineReader**

<!---->

* **Writer**
  * **CharArrayWriter**
  * **OutputStreamWriter**
    * **FileWriter**
  * **PipedWriter**
  * **FilterWriter**
  * **BufferedWriter**
  * **StringWriter**
  * **PrintWriter**

</details>

字节流读取单个字节，字符流读取单个字符(一个字符根据编码的不同，对应的字节也不同，如 UTF-8 编码中文汉字是 3 个字节。)

## **从**数据来源的角度

从数据来源或者说是操作对象角度看，IO 类可以分为：

<details>

<summary>文件</summary>

* **FileInputStream**
* **FileOutputStream**
* **FileReader**
* **FileWriter**

</details>

<details>

<summary>数组</summary>

* **ByteArrayInputStream**
* **ByteArrayOutputStream**
* **CharArrayReader**
* **CharArrayWriter**

</details>

<details>

<summary>管道</summary>

* **PipedInputStream**
* **PipedOutputStream**
* **PipedReader**
* **PipedWriter**

</details>

<details>

<summary>基本数据类型</summary>

* **DataInputStream**
* **DataOutputStream**
* **StringReader**
* **StringWriter**

</details>

<details>

<summary>缓冲操作</summary>

* **BufferedInputStream**
* **BufferedOutputStream**
* **BufferedReader**
  * **LineNumberReader**
* **BufferedWriter**

</details>

<details>

<summary>打印</summary>

* **PrintStream**
* **PrintWriter**

</details>

<details>

<summary>序列化与反序列化</summary>

* **ObjectInputStream**
* **ObjectOutputStream**

</details>

<details>

<summary>转换</summary>

* **InputStreamReader**
* **OutputStreamWriter**

</details>
