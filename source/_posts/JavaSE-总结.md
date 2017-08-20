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


[原文链接](http://docs.oracle.com/javase/tutorial/)
***
# Java技术
***
在Java编程语言中，Java源代码都首先被写在一个拓展名为**.java**的文本文件中，这些源代码被javac编译器编译为**.class**文件，也被称为字节码文件，编译后的字节码文件是由机器语言也就是二进制组成它，使用**Java虚拟机**——**Java Virtual Machine**(**JVM**)来运行字节码文件。
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
对象隐藏自己的状态并且只能通过方法进行交互，这被称作**数据封装**（Data Encapsulation），这是面向对象编程的一个基本原则。

真实世界可能有成百上千个同一种类型的对象，可以把同一种类型的对象抽象归纳为一个类。类可以看作对象的蓝图，每个对象都依靠类来创建，创建的对象也被称为类的实例（instance ）
***
# 语言基础
***
## 变量
***
Java语言同时使用了**字段（Field）**和**变量（Variable）**两个概念，应该来说字段是变量的一种，Java编程语言中包含以下几种变量：
- **实例变量（非静态字段）**：对象在“非静态字段”中存储它们的独立状态，非静态字段是不用**static**关键字修饰的字段，也被称为实例变量。对于一个类的不同实例，它们的值都是独立的。实例变量是与实例相关的
- **类变量（静态字段）**：类变量是使用**static**修饰的字段，也叫静态字段，静态字段是随着类加载而加载，并存在于静态区中，一个类的不管有多少实例，它们都共享该类的静态字段。类变量是与类相关的。
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
|double|0.0d|
|char|'\u0000'|
|String (or any object) | null|
|boolean|false|

局部变量不会被自动赋值，再使用局部变量前要确保被赋值，不然会报编译错误。

### 字面值
字面值是一个固定值的源代码表示，可以分配一个字面值给相应的原始类型。
一个整数型字面值如果以**L**或**l**结尾，则表示long类型，否则表示int类型，建议使用L，以免误认。byte, short, int, 和long的字面值都可以使用int类型字面值创建。类型long的值超过int的范围可以从long字面值创建。整型字面值可以以三种数字系统表示：
- 十进制：最常用进制
- 十六进制：字面值前加**0x**
- 二进制：字面值前加**0b**，在JavaSE 7之后支持二进制字面值

一个浮点数字面值如果以**F**或**f**结尾，则表示float类型，否则是double类型，double类型字面值也可以用**D**或**d**结尾。浮点数字面值还可以使用**E**或**e**科学表达式。

char或String类型的字面值可能包含Unicode(UTF-16)字符，也可以使用Unicode转义，如'\u0108'。

最后还有一个特殊的字面值 ，叫class字面值，任何一种类型后加“.class”，如String.class，这指表示类型的对象（Class类型）。

> Java SE 7之后数值字面值可以插入下划线（_）以增强可读性。

### 数组
数值是一个原始类型或对象的容器，数据长度在创建时指定，并且之后是固定的。数组内的项目被称为元素，通过索引获取，索引从0开始。

数组声明有两种形式，***type[] anArray*** 或 ***type anArray[]***，数组初始化也有两种形式，一种使用new关键字new type[length] 另一种直接分配值***{type,type,type...}***。Java还支持多维数组，Java中多维数组每行长度可以任意。获取数组长度可以使用数组的内置属性**length**。

## 运算符
***
### 赋值运算符

|运算符|说明|
|----|----|
|=|简单赋值赋值|

### 算数运算符

|运算符|说明|
|----|----|
|+|加（也用于字符串拼接）|
|-|减|
|*|乘|
|/|除|
 |%|取余|
 
 你也可以将算数运算符与赋值运算符结合为复合赋值符，如x+=1 与 x=x+1结果相同，不同的是复合赋值符隐式包含了一个类型强转。

### 一元运算符

|运算符|说明|
|----|----|
|+|一元加运算符，表示正数|
|-|一元减运算符，表示负数|
|++|自增运算符，表示加1|
|--|自减运算符，表示减1|
|!|逻辑非运算符，反转boolean值|

自加/自减运算符可以用作操作数前缀或后缀，虽然最终结果一样，但做前缀时（++result）时，评估为递增值，后评估。做后缀时（result++），评估为原始值。

 
### 关系运算符

|运算符|说明|
|----|----|
|==|等于|
|!=|不等于|
|>|大于|
|>=|大于等于|
|<|小于|
|<=|小于等于|

 ### 条件运算符

|运算符|说明|
|----|----|
|&&|在两个boolean表达式之间执行AND|
|&#124;&#124;|在两个boolean表达式之间执行OR|
|?:|三元运算符，if-then-else语句缩写|

三元运算符格式为 ** result = someCondition ? value1 : value2;**，它可以解读为，如果someCondition为ture，那么给result赋值value1，否则赋值value2。

### 类型比较运算符

|运算符|说明|
|----|----|
|instanceof|将对象与指定的类型进行比较|

当使用instanceof运算符时，左面的操作元是一个对象,右面是一个类.当左面的对象是右面的类创建的对象时,该运算符运算的结果是true,否则是false，instanceof左边操作元显式声明的类型与右边操作元必须是同种类或右边是左边父类的继承关系,不同的继承关系下,编译出错  请记住，null不是任何类的实例。

### 位和位移运算符

|运算符|说明|
|----|----|
|~|一元位运算符，反转每个位|
|<<|有符号左移位运算符，|
|>>|有符号右移位运算符，|
|>>>|无符号右移运算符|
|& |位与运算符|
|^|位异或运算符|
|&#124;|位或运算符|

位和位移运算符只能操作整型数据。

### 运算符优先权

|运算符|	优先度（从高到低）|
|--|--|
|postfix	|expr++ expr--
|一元|	++expr --expr +expr -expr ~ !
|multiplicative|	* / %
|加减|	+ -
|位移|	<< >> >>>
|关系|	< > <= >= instanceof
|等于|	== !=
|位AND|	&
|位 exclusive OR|	^
|位 inclusive OR|&#124;
|逻辑 AND	|&&
|逻辑 OR	|&#124;&#124;
|三元	|? :
|赋值|	= += -= *= /= %= &= ^= |= <<= >>= >>>=



## 表达式，语句和代码块
***
### 表达式
表达式是由**变量**，**运算符**和**方法调用**组成的构造，它们根据语言的语法进行构造，并被评估为单个值。表达式返回的数据类型由表达式使用的元素决定。*复合表达式建议使用"()"来使代码易读和易于维护*。

### 语句
语句大致相当于自然语言中的句子。一个语句形成一个完整的执行单元。以下类型的表达式可以通过用分号（;）终止表达式来形成语句。
- 赋值表达式
- ++或--的使用
- 方法调用
- 对象创建表达式

这样的语句被称为**表达式语句**。除了**表达式语句**，还有两种语句：**声明语句**和**流程控制语句**。

### 代码块
一个代码块是平衡大括号之间的一组零个或多个语句，可以在任何允许单个语句使用的地方使用。以下示例BlockDemo说明了代码块的使用：
```java
class BlockDemo {
     public static void main(String[] args) {
          boolean condition = true;
          if (condition) { // begin block 1
               System.out.println("Condition is true.");
          } // end block one
          else { // begin block 2
               System.out.println("Condition is false.");
          } // end block 2
     }
}
```

## 流程控制语句
***
流程控制语句可以改变默认的代码执行顺序，它包括**决策语句**（if-then, if-then-else, switch），**循环语句**（for, while, do-while）和**分支语句**（break, continue, return）。

### 决策语句
if语句形式
```java
// if-then 语句1
   if(condition){
            execute statement...;
        }
		
//  if-then 语句2
   if(condition)
            execute statement...;
			
			
//  if-then-else 语句1
   if(condition){
            execute statement1...;
        }else{
            execute statement2...;
        }
		
//  if-then-else 语句2
   if(condition1){
            execute statement1...;
        }else if(condition1){
            execute statement2...;
        }...		
       
```
switch语句形式
```java
switch语句1
switch (value) {
            case value1:  
				statement1;
                break;
            case value2:  
				statement2;
                break;
            case value3:  
				statement3;
                break;
				...
            default: statementN;
                break;
				
switch语句2
switch (value) {
            case value1:  
            case value2:  
				statement2;
                break;
            case value3:  
				statement3;
                break;
				...
            default: statementN;
                break;				
```
switch语句支持**byte**,**short**,**char**,**int**原始类型，**Enum**类型，**String**（Java SE 7之后），以及**Character**,**Byte**,**Short**,**Integer**包装类型。switch块中break语句终止包含它的switch语句，如果没有break语句，匹配case标签之后的所有语句都将按顺序执行，而不管后续case标签的表达，直到遇到break语句。

### 循环语句
while语句
```java
while (expression) {
     statement(s)
}
```
do-while语句
```java
do {
     statement(s)
} while (expression);
```
for语句
```java
for (initialization; termination; increment) {
    statement(s);
}

// 无限循环
for ( ; ; ) {
    statement(s)
}
```
### 分支语句
**break**语句有两种形式：标签和无标签。标签break语句将流程控制从被标签的语句之后的语句开始执行。
**continue**语句跳过for，while或do-while循环的当前迭代。它和break一样也有两种形式，有标签和无标签。
**return**语句从当前方法退出，并且控制流程返回到调用该方法的位置。return语句有两种形式：一种返回一个值，一个不返回。

***
# 类与对象
***
## 类
***
### 方法
方法签名是指方法的名称和参数类型，Java通过方法签名区分两个不同的方法。Java编程语言支持方法重载，也这是方法之间可以有相同的名称，但是参数数量或类型不同。方法重载应该谨慎使用，因为它们可以使代码的可读性降低。
### 构造方法
编译器会为没有构造方法的类提供一个默认的无参构造，这个默认无参构造会调用父类的无参构造，这时要注意，如果父类没有无参构造，会产生编译器错误。