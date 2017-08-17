title: JavaSE 总结
id: 1502951163806
author: 不识
date: 2017-08-17 14:26:09
tags:
---
<style>
strong {
    margin: 2px;
    background-color: #f2f2f2;
    border: 1px solid #ccc;
    border-radius: 4px;
    padding: 1px 3px 0;
    text-shadow: none;
    white-space: nowrap;
	color: #6d180b;
	font-weight: normal;
}

.quote{
	border: 1px solid #ccc;
	background-color:#f8f8f8;
	padding:20px;
}
</style>

***
# Java技术
***
在Java编程语言中，Java源代码都首先被写在一个拓展名为**.java**的文本文件中，这些源代码被javac编译器编译为**.class**文件，也被称为字节码文件，编译后的字节码文件是由机器语言也就是二进制组成它，使用Java虚拟机——**Java Virtual Machine**(JVM)来运行字节码文件。
![compile](/images/java base/getStarted-compiler.gif)
因为**.class**文件是在JVM上运行，而JVM又适配不同的操作系统，所以Java语言是跨平台的，
![running on multiple platforms.](/images/java base/helloWorld.gif)
Java程序不是在底层硬件上直接运行，运行Java程序的被称为Java平台——Java Platform，它由两部分组成：
- The Java Virtual Machine
- The Java Application Programming Interface (API)

Java平台是完全的软件平台，运行在各种底层硬件平台上，Java程序也通过Java平台与底层硬件平台实现隔离，也因此Java平台运行要比本地代码慢些，但随着JVM的优化和性能提升，它已经接近本地代码的性能。
![jvm](/images/java base/getStarted-jvm.gif)

***
# 面向对象编程概念
***
真实世界的对象由两个特性组成：**状态**和**行为**。面向对象编程通过一个程序对象（Object）模拟真实世界的对象，一个Object在它的**Field**（字段）（一些其他编程语言叫变量）中存储状态，并且通过**Method**（方法）（一些其他编程语言叫函数）来暴露行为。
![concepts-object](/images/java base/concepts-object.gif)
对象隐藏自己的状态并且只能通过方法进行交互，这被称作数据封装（Data Encapsulation），这是面向对象编程的一个基本原则。

真实世界可能有成百上千个同一种类型的对象，可以把同一种类型的对象抽象归纳为一个类。类可以看作对象的蓝图，每个对象都依靠类来创建，创建的对象也被称为类的实例（instance ）
***
# 语言基础
***
## 变量
***
Java语言同时使用了**字段（Field）**和变量**（Variable）**两个概念，应该来说字段是变量的一种，Java编程语言中包含以下几种变量：
- **实例变量（非静态字段）**：对象在“非静态字段”中存储它们的独立状态，非静态字段是不用**static**关键字修饰的字段，也被称为实例变量。对于一个类的不同实例，它们的值都是独立的。
- **类变量（静态字段）**：类变量是使用**static**修饰的字段，也叫静态字段，静态字段是随着类加载而加载，并存在于静态区中，一个类的不管有多少实例，它们都共享该类的静态字段。
- **局部变量**：局部变量存在于方法中，用于存储临时值。局部变量仅在声明它的方法内可见，
- **参数**：方法接收的变量被称为参数。

不同的编程语言对变量命名由不同的规则，Java编程语言有一些对变量名称的要求和惯例；
- 变量名称可以由字母，数字，"$"符号，"_"下划线组成，并且第一字符不能是数字，通常是字母，并且“$”符基本不使用。
- 变量名称大小写敏感，在相同作用域内不能有重复，并且不能使用Java关键字或保留关键字作为变量名。
- 作为惯例，变量由多个单词组成时，采用驼峰命名法，首字母小写。如果变量存储一个常量，使用纯大写字符作为名称，并使用下划线分割单词，如static final int NUM_GEARS = 6。惯例，下划线不会在其他地方使用。

### 原始数据类型
Java编程语言支持八种原始数据类型，分别是：
- 整数型
	- **byte**：byte数据类型是一个占用1个字节，有符号的整数。它的值范围是-128≤boolean<127。
	- **short**：short数据类型是一个占用2个字节，有符号的整数。它的值范围是-32,768≤short< 32,767。
	- **int**：int数据类型是一个占用4个字节，有符号的整数。它的值范围是-2<sup>31</sup>≤int< 2<sup>31</sup>-1。在Java 8之后，可以使用int数据类型表示32-bit的无符号整数，范围从0到2<sup>31</sup>-1。
	- **long**：long数据类型是一个占用8个字节，有符号的整数。它的值范围是-2<sup>63</sup>≤long< 2<sup>63</sup>-1。在Java 8之后，可以使用long数据类型表示64-bit的无符号整数，范围从0到2<sup>64</sup>-1。
- 浮点型
	- **float**：float数据类型是一个单精度，占4个字节的浮点数。这个数据类型不应该用于精确值，比如货币。请使用java.math.BigDecimal代替。
	- **double**：double数据类型是一个双精度，占8个字节的浮点数。对于小数值，这个数据类型是默认选择。和float一样，它也不应该用于精确值，例如货币。
- 布尔型
	- **boolean**：boolean类型只有两种可能值true和false。在JVM中没有boolean类型专用指令，编译后boolean类型实际使用int类型代替，它占4字节，这是为了CPU存取的高效性，但是使用boolean数组时，为了节约内存，它又会被编译为byte数组，每个boolean元素占1个字节。
- 字符型
	- **char**：char数据类型是一个占2个字节的 Unicode字符。它的范围从'\u0000' (or 0)到'\uffff' (or 65,535 不包含)。

### 变量初始化
没有赋值的字段初始化时会被自动赋予一个默认值，下表是默认赋予的值：

|数据类型|默认值（字段）|
|-----|------|
|byte|0|
|short|0|
|int|0|
|long|0L|
|float|0.0f|
|double|	.0d|
|char|	'\u0000'|
|String (or any object) | null|
|boolean|false|

局部变量不会被自动赋值，再使用局部变量前要确保被赋值，不然会报编译错误。

### 字面值
字面值是一个固定值的源代码表示，可以分配一个字面值给相应的原始类型。
一个整数型字面值如果以L或l结尾，则表示long类型，否则表示int类型，建议使用L，以免误认。byte, short, int, 和long的字面值都可以使用int类型字面值创建。类型long的值超过int的范围可以从long字面值创建。整型字面值可以以三种数字系统表示：
- 十进制：
- 十六进制：
- 二进制：

## 操作符
***


## 表达式，语句和代码块
***

## 流程控制语句
***











