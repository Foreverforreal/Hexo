title: JavaSE 总结
id: 1502951163806
author: 不识
date: 2017-08-17 14:26:09
tags:
---
<style>

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
一个整数型字面值如果以**L**或**l**结尾，则表示**long**类型，否则表示**int**类型，建议使用**L**，以免误认。**byte**,**short**,**int**,和**long**的字面值都可以使用int类型字面值创建。类型long的值超过int的范围可以从long字面值创建。整型字面值可以以三种数字系统表示：
- 十进制：最常用进制
- 十六进制：字面值前加**0x**
- 二进制：字面值前加**0b**，在JavaSE 7之后支持二进制字面值

一个浮点数字面值如果以**F**或**f**结尾，则表示**float**类型，否则是**double**类型，**double**类型字面值也可以用**D**或**d**结尾。浮点数字面值还可以使用**E**或**e**科学表达式。

**char**或**String**类型的字面值可能包含Unicode(UTF-16)字符，也可以使用Unicode转义，如'\u0108'。

最后还有一个特殊的字面值 ，叫class字面值，任何一种类型后加“.class”，如String.class，这指表示类型的对象（Class类型）。

> Java SE 7之后数值字面值可以插入下划线（_）以增强可读性。如long a = 777_777_1;

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
- ++或\-\-的使用
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
//  switch语句1
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
            default: 
			statementN;
                break;
				
//  switch语句2
switch (value) {
            case value1:
            case value2:  
			statement2;
                break;
            case value3:
		statement3;
                break;
				...
            default: 
			statementN;
                break;				
```
switch语句支持**byte**,**short**,**char**,**int**原始类型，**Enum**类型，**String**（Java SE 7之后），以及**Character**,**Byte**,**Short**,**Integer**包装类型。switch块中break语句终止包含它的switch语句，如果没有break语句，匹配case标签之后的所有语句都将按顺序执行，而不管后续case标签的表达，直到遇到break语句。

### 循环语句
//  while语句
```java
while (expression) {
     statement(s)
}
```
//  do-while语句
```java
do {
     statement(s)
} while (expression);
```
//  for语句
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
#### 参数
**方法签名**是指方法的名称和参数类型，Java通过方法签名区分两个不同的方法。Java编程语言支持**方法重载**，也这是方法之间可以有相同的名称，但是参数数量或类型不同。方法重载应该谨慎使用，因为它们可以使代码的可读性降低。
**Parameters**和**Arguments**都是指参数，但是**Parameters**是方法声明中的变量列表，是形式参数，而**Arguments**是调用方法时传入的变量，是实际参数。
当需要向一个方法传入不确定数量的参数时，可以使用可变参数***varargs***，它实际是数组参数的一种简写形式。可变参数形式如下
```java
public void methodName(int... varargsName){...}
```

#### 返回值
一个方法在以下三种情况下返回
- 执行方法内所有语句
- 遇到一个return语句
- 抛出一个异常

使用void声明的方法没有返回值，不需要return语句，但也可以使用用来控制流程退出方法，此时不能有返回值，否则编译器会报错。

### 构造方法
编译器会为没有构造方法的类提供一个默认的无参构造，这个默认无参构造会调用父类的无参构造，这时要注意，如果父类没有无参构造，会产生编译器错误。

### this关键字
在实例方法或者构造方法中，this是对当前对象（被调用实例方法或构造方法的对象）的引用。你在实例方法或构造方法中可以通过this引用当前对象的任何成员。
#### 引用字段
使用this引用字段一般用在字段被参数或局部变量遮蔽时，通常使用在构造方法中，不能用在静态方法中。引用字段使用**this.X**语法，如下
```java
public class Point {
    public int x = 0;
    public int y = 0;
        
    //constructor
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
#### 引用方法
使用this引用方法类似于引用字段，引用方法使用**this.method(arg1,arg2...)**语法,this可以引用静态方法，但是不能用在静态方法中。不过一般不使用this引用方法，而是直接调用。

#### 引用构造方法
this还可以引用构造方法，此时this语句必须在一个构造方法中引用另一个构造方法。这样的调用被称为显式构造方法调用。**当使用this调用另一个构造方法时，this语句必须方法在构造方法中的第一行**。如下：
```java
public class Rectangle {
    private int x, y;
    private int width, height;
        
    public Rectangle() {
        this(0, 0, 1, 1);
    }
    public Rectangle(int width, int height) {
        this(0, 0, width, height);
    }
    public Rectangle(int x, int y, int width, int height) {
        this.x = x;
        this.y = y;
        this.width = width;
        this.height = height;
    }
    ...
}
```
#### 引用更大域内的成员
当使用内部类时，如果内部类的成员遮蔽了外部类的成员，要想引用外部类的成员，可以使用以下语法：
```java
OutClassName.this.X
```

### 类成员访问控制
访问级别修饰符决定其他类是否可以使用一个特定字段或调用特定方法。有两个级别的访问控制：
- 类级：public或package-private（没有明确的修饰符）
- 成员级：public，private，protected或package-private（没有明确的修饰符）

使用public修饰的类可以在任何地方可见，一个类没有修饰符（默认package-private权限）只能在本包内可见。

成员级上的修饰符多了两种权限，private和protected。private修饰符指定该成员只能在本类访问，而protected修饰符指定该成员只能在它本包中进行访问（与package-private一样），另外还可以通过在其他包中的子类进行访问。

下表显示了每个修饰符允许的成员访问

|修饰符|同类中|同包中|不同包子类|所有|
|--|---|--|--|--|
|public|O|O|O|O|
|protected|O|O|O|X|
|默认|O|O|X|X|
|private|O|X|X|X|

如上可见，同类中总是可以访问它的成员，同包中只有private修饰的类成员无法在类外访问，该包之外声明的类的子类可以访问public或protected权限的成员，只有public修饰的成员可以被所有类访问。

### 类成员
#### 类变量
**类变量**也叫**静态字段**，也就是使用**static**关键字修饰的字段，静态字段被该类的所有的实例共享。一个类的类字段可以不用创建对象来引用，而是直接使用类名引用，使用**ClassName.X**语法。最好使用类名引用，而不是用对象，因为这样使类变量角色更加清晰。
#### 类方法
**类方法**也叫**静态方法**，也就是方法声明中有**static**修饰符的方法。它也可以使用类名直接调用，并且应当这样做。静态方法的常见用途是访问静态字段。

注意，不是所有的实例和类的变量与方法都能组合使用**
- **实例方法可以直接访问实例变量和实例方法**。
- **实例方法可以直接访问类变量和类方法**。
- **类方法可以直接访问类变量和类方法**。
- **类方法不可以直接访问实例变量或实例方法**——它们必须使用对象引用。同时，类方法也不能使用**this*  *关键字，因为类方法与实例无关，它没有this可以引用的实例。

#### 常量
**static**修饰符与**final**结合使用用于定义常量，**final**表示这个字段的值不能再被改变。以这种方式定义的常量不能再重新分配，否则会产生一个编译时期错误。按照惯例，常量名字母全部大写。如果由多个单词组成，使用下划线（_）分隔。
> 如果将原始类型或string定义为常量，并且它的值在编译时期已知。编译器会将代码中的常量名替换为它的值。这被称为编译时期常量（compile-time constant）。如果源代码中修改常量值，需要重新编译所有使用了该常量的类。

### 字段初始化
#### 静态字段初始化
通常将字段声明和初始化放在一起，但有时字段初始化需要一些逻辑处理（例如，错误处理或for循环来填充复杂数组），则需要分开。实例变量可以在构造方法中初始化，以添加一些逻辑。对于类变量，由于它是和类相关的，Java编程语言使用**静态初始化代码块**（static initialization blocks）提供此功能。其形式如下：
```java
static {
    // 进行初始化处理
}
```
一个类可以有多个静态初始化代码块，并且可以在类体内任意位置，运行时期系统按照它们在源代码中的顺序进行调用。
此外也可以使用一个私有静态方法来初始化静态变量字段。
```java
class Whatever {
    public static varType myVar = initializeClassVariable();
        
    private static varType initializeClassVariable() {

        // initialization code goes here
    }
}
```
私有静态方法的优点是，如果需要重新初始化类变量，它们可以稍后重用。

#### 实例成员初始化
实例变量除了可以在构造方法中初始化外，还有两个选择：**初始化代码块**和**final方法**。

初始化代码块就像静态初始化代码块一样，除了没有static关键字：
```java
{
    // whatever code is needed for initialization goes here
}
```

<font style="color:#ec70ae;">Java编译器会将初始化代码块复制到每个构造方法中。</font>  

因此，这种方法可以用于在多个构造方法之间共享一个代码块。

final方法不可以在子类中重写，所以它也可以用于初始化实例变量：
```java
class Whatever {
    private varType myVar = initializeInstanceVariable();
        
    protected final varType initializeInstanceVariable() {

        // initialization code goes here
    }
}
```
如果子类想要重用初始化方法时，这特别适用。


## 对象
***

对象创建语句：
```java
Object varName = new Object(args1,args2);
```
每个对象创建语句都包含三部分
1. 声明：Object varName属于变量声明部分，不像原始类型变量声明，引用类型声明不会为其分配内存空间。
2. 实例化：new关键字是一个Java操作符，它为变量分配内存并返回分配的内存的引用，它还同时调用对象构造方法
3. 初始化：new操作符后跟着一个 构造方法调用，它初始化这个新对象。

## 嵌套类
***
嵌套类分为两种：静态和非静态。使用static声明的嵌套类称为静态嵌套类。非静态嵌套类称为内部类。
嵌套类是包含它的类的成员。非静态嵌套类（内部类）可以访问其他的类成员，即使是被private修饰的。静态嵌套类则无法访问其他类成员（实例成员）。作为外部类（OuterClass）的成员，嵌套类可以使用private, public, protected, 或package private来声明。
使用嵌套了有以下三种理由：
- **它是将只会在一个地方使用的类逻辑分组的方式。**如果一个类只对另一个类有用，，那么把它嵌入该类并将它们保持在一起是合乎逻辑的。嵌套的例如”助手类“使它们的包结构更加精简。
- **它增加了封装性。**考虑两个顶级类，A和B，B需要访问A需要声明尾private的成员。通过将类B藏在类A中，A的成员可以声明为private，而B还可以访问它们。此外，B本身也可以从外界隐藏起来。
- **它可以导致更可读和可维护的代码：**在顶级类中嵌套小类将代码更接近使用的位置。

### 静态嵌套类
与类方法和类变量一样，静态嵌套类是与其外部类相关的。就像静态类方法，静态嵌套类不能直接引用包含它的类的实例变量或方法。只能通过对象引用来使用。静态嵌套类可以用包含它的类的类名来访问：
```java
OuterClass.StaticNestedClass
```
例如，要想创建静态嵌套类的对象，使用以下语法：
```java
OuterClass.StaticNestedClass nestedObject = new OuterClass.StaticNestedClass();
```

### 内部类
与实例方法和实例变量一样，内部类是与包含它的类的实例相关的，并且可以直接访问对象的方法和字段。<font style="color:#ec70ae;">同时，由于内部类是与实例相关，所以它不能定义任何静态成员</font>。
内部类的实例只能存在于外部类的实例中，要想实例化一个内部类，必须首先实例化外部类。然后在外部对象中创建内部对象，如下语法：
```java
OuterClass.InnerClass innerObject = outerObject.new InnerClass();
```
还有两种特殊的内部类：**局部类**和**匿名类**。

> 强类不建议序列化内部类，不然在不同的JRE实现反序列化时可能出现不兼容性。

#### 局部类
　　局部类是定义在代码块（如for循环，if字句或方法）中的类，通常定义在方法体中。局部类可以访问包含它的类中的成员。<font style="color:#ec70ae;">此外，局部类还可以访问局部变量或方法参数，但是局部类只能访问声明为final的变量。</font>局部类访问的局部变量或参数叫做捕获变量（captured variable），局部类会复制一份该变量，为了保持两份变量一致性，所以只能访问final修饰的，**但在Java 8 之后局部类还可以访问事实上不变（effectively final）的变量，也就是虽然没有用final修饰，但是局部类内没有修改它的值**。
　　局部类类似于内部类，不能定义或声明任何静态成员，在静态方法中的局部类只能引用包含它的类的静态成员。
　　局部类是非静态的，因为它们可以访问包含它们的类的实例成员。因此，它们不能包含大多数类型的静态声明。
　　不能在代码块中声明接口，因为接口本质上是静态的。也不能在局部类中声明静态初始化器或成员接口。但是局部类可以具有静态成员，只要它们是常量变量（常量变量是原始类型或类型的字符串，它被声明为final，并使用编译时常量表达式初始化。）。

#### 匿名类
匿名类能够同时声明和实例化一个类。匿名类表达式包含以下内容：
- new操作符
- 要实现的接口或要继承的类的名字。
- 包含构造方法参数的括号，就如普通类实例创建表达式。注意：当实现接口时没有构造方法，所以可以使用一个空的括号。
- 类声明体。

就像局部类，匿名类也能捕获变量；它们对包含它们的域中的局部变量有相同的访问权限：
- 匿名类可以访问包含它的类的成员；
- 匿名类不能访问包含它们域中不是final或事实final的变量；
- 就如嵌套类，匿名类中的声明类型（比如变量）会遮蔽它们所处域中同名的声明。

匿名类对于它们的成员有和局部类一样的限制
- 不可以在匿名类中声明静态初始化器或者成员接口
- 匿名类可以有静态成员，只要它们是常量变量。

请注意，您可以在匿名类中声明以下内容：
- 字段
- 额外的方法（即使他们没有实现任何超类型的方法）
- 实例初始化器
- 局部类

**但是，你不可以在匿名类中声明构造方法**。   

#### lambda表达式
与局部类与匿名类一样，lambda表达式可以捕获变量，并且对封闭域内的局部变量有相同的访问权限，只能访问final或事实fianl的局部变量。然而，不像局部类和匿名类，lambda表达式不会有任何变量遮蔽问题，Lambda表达式是词法上确定范围。这意味着它们不会从超类型继承任何名称或引入新的域级别。在lambda表达式中的声明就像在包含它的域中声明一样。


### 嵌套类适用场景

- **局部类**：如果需要创建一个类的多个实例，访问其构造方法或引入新的命名类型（因为，例如，以后需要调用其他方法），请使用它。
- **匿名类**：需要声明字段或额外方法的时候使用它
- **Lambda表达式**
	- 如果需要封装单个行为单位，并传递给其他代码，请使用它
	- 如果需要一个函数接口的简单实例，并且不适用前面的条件（例如，你不需要构造函数，命名类型，字段或其他方法）
- **嵌套类**：如果类似于局部类的要求，并希望其类型更广泛地可用，并且不需要访问局部变量或方法参数，使用它。
	- 如果需要访问包含它的实例的非公开字段和方法，使用非静态嵌套类，否则使用静态嵌套类。
	
## 枚举类型
***
枚举类型（ enum type）是一种特殊的数据类型，可使变量成为一组预定义的常量。因为是常量，所以枚举类型字段名称都是大写字母。使用enum关键字定义枚举类型。
> 所有枚举隐式继承了java.lang.Enum。因为一个类之类继承一个父类，Java不支持多继承，所以枚举无法继承其他类。

枚举中常量需要先于字段和方法继承。常量要以分号结尾。枚举的构造方法必须是**package-private**或**private**权限。它会自动创建在枚举正文开头定义的常量。不能自己调用枚举的构造方法。

***
# 接口和继承
***
## 接口
***
在Java编程语言中，接口是类似于类的引用类型，只能包含常量，方法签名，default方法，static方法和嵌套类型。只用default方法和static方法拥有方法体。接口不能被实例化，它们只能被类实现，或被其他接口继承。

### 定义接口
接口定义语法如下：
```java
public interface Name extend ParentInterface1,ParentInterface2...{
		void method1();
		default mehtod2(){
			//code...
		}
		static void method3(){
			//code...
		}
}
```
和类不同，接口可以继承多个父接口。使用逗号（,）分割多个父接口。接口体可以包含**abstract方法**,**default方法**以及**static方法**。abstract方法后跟一个分号，但没有大括号（抽象方法不包含实现）。接口中所有的abstract，default，static方法隐式使用public修饰，所以public可以忽略。

此外接口可以包含常量声明。接口中所有的常量值隐式使用public，static，和final修饰，所以这些也可以忽略。

### 实现接口
一个类可以实现多个接口，使用implements关键字实现接口。多个接口使用逗号（,）分割，如果这个类同时还继承其他类，按惯例，implements字句放在extend字句后。

### Dafault方法
如果有一个接口名为DoiT：
```java
public interface DoIt {

   void doSomething(int i, double x);
   int doSomethingElse(String s);
}
```
现在想向这个接口中添加第三个方法，直接添加的话会破坏类结构，因为原来实现该接口的类都必须实现新的方法。为了避免这个问题有两种解决方法。
第一种是创建新的接口继承DoIt：
```java
public interface DoItPlus extends DoIt {

   boolean didItWork(int i, double x, String s);
   
}
```
这样你的代码可以选择使用旧接口或者新的升级接口。
第二种是在原来接口中将新添方法定义为default方法：
```java
public interface DoIt {

   void doSomething(int i, double x);
   int doSomethingElse(String s);
   default boolean didItWork(int i, double x, String s) {
       // Method body 
   }
   
}
```
注意，必须为default方法提供实现。此外还可以定义一个新的static方法到已有接口中。原来这样实现该接口的类不必修改或重新编译以适应新添的方法。

当另一个接口继承一个拥有default方法的接口，你可以做以下事：
- 没有提到default方法，这允许扩展的接口继承default方法。
- 重新声明default方法，使它变为abstract。
- 重新定义default方法，这回重写它。

### Static方法
除了default方法之外，还可以在接口中定义静态方法。（静态方法是与定义它的类相关联的方法，而不是与任何对象相关联。类的每个实例都共享其静态方法。）这样更容易在库中组织辅助方法;可以在同一个接口中指定静态方法到接口上，而不是分开的类上。

## 继承
***
子类会从父类继承所有的**public**和**protected**成员（字段，方法，嵌套类），无论子类的包在哪。如果子类和父类同包，它还可以继承**package-private**成员。构造方法不是成员，所以不会继承，但是可以从子类调用父类的构造方法。以下是子类可以在继承关系中执行的操作：
- 可以直接使用继承字段
- 可以在子类声明和父类字段相同名称的字段，这样隐藏了父类该字段
- 可以声明一个父类中没有的字段
- 可以直接使用继承的方法
- 可以在子类写一个新的实例方法，与父类中的有相同方法签名，这样隐藏了父类该方法
- 可以在子类写一个新的static方法，与父类中的有相同方法签名，这样隐藏了父类该方法
- 可以声明一个父类中没有的新方法
- 可以写一个子类构造方法，隐式调用或通过使用super关键字。来调用父类中的构造方法。

子类不能继承父类的private成员，但是可以通过父类public或protecteds方法间接访问。由于嵌套类可以访问包含它的类的所有成员，包括private，所以也可以通过public或protected的 嵌套类间接访问父类private成员。

### 接口多继承
<font style="color:#ec70ae;">类与接口的一个显著区别是，类有字段而接口不可以有字段，因为对象使用字段保存状态，Java编程语言不允许多继承的一个原因就是避免状态的多继承 问题。因为接口没有字段，所以不会有状态的多继承问题。</font>

**实现的多继承**（Multiple inheritance of implementation）是从多个类中继承方法定义的能力，但也因此会产生一些问题。比如，一个接口多个父类中包含冲突和歧义的名称。此外，default方法也引入了一种实现的多重继承形式。一个类可以可以实现多个接口，接口中可能包含相同名称的default方法。Java编译器提供了一些规则来确定特定类使用的default方法。

Java编程语言支持**类型的多继承**（multiple inheritance of type），它是类实现多个接口的能力。一个对象可能有多个类型：它自身类的类型，它实现的多个接口的类型。

### 方法重写与隐藏
#### 实例方法
与父类方法拥有有相同签名（名称，以及其参数的数量和类型）的子类实例方法重写了父类方法。

**重写方法**（overriding method ）和它重写的方法有相同的名称，参数的数量和类型，返回类型。一个重写方法还可以返回一个被重写方法返回类型的子类型。这个子类型称为**协变返回类型**（covariant return type）。

如果想要重写一个方法，可以使用**@Override**注解指示编译器要重写父类的方法。

#### 静态方法
如果子类定义一个静态方法和父类中的静态方法有相同的签名，那么子类中的方法隐藏（hide）了父类中的对应方法。

隐藏静态方法和重写实例方法之间的区别有重要意义：
- **被调用的重写实例方法版本是在子类中的那一个。**
- **被调用的隐藏实例方法版本取决于它是用父类中调用还是子类中调用。**

考虑下面一个例子包含了两个类：
```java
public class Animal {
    public static void testClassMethod() {
        System.out.println("The static method in Animal");
    }
    public void testInstanceMethod() {
        System.out.println("The instance method in Animal");
    }
}

public class Cat extends Animal {
    public static void testClassMethod() {
        System.out.println("The static method in Cat");
    }
    public void testInstanceMethod() {
        System.out.println("The instance method in Cat");
    }

    public static void main(String[] args) {
        Cat myCat = new Cat();
        Animal myAnimal = myCat;
        myAnimal.testClassMethod();
        myAnimal.testInstanceMethod();
    }
}
```
Cat类覆盖了Animal中的实例方法，并隐藏了在Animal中静态方法。控制台输出：
```
The static method in Animal
The instance method in Cat
```

#### 接口方法
接口中的default方法和abstract方法像实例方法一样被继承。但是，当类或接口的父类型提供具有相同签名的多个default方法时，Java编译器遵循继承规则来解决名称冲突。这些规则是由以下两个原则驱动的：
- **实例方法优于接口default方法。**
考虑以下类和接口
```java
public class Horse {
    public String identifyMyself() {
        return "I am a horse.";
    }
}
public interface Flyer {
    default public String identifyMyself() {
        return "I am able to fly.";
    }
}
public interface Mythical {
    default public String identifyMyself() {
        return "I am a mythical creature.";
    }
}
public class Pegasus extends Horse implements Flyer, Mythical {
    public static void main(String... args) {
        Pegasus myApp = new Pegasus();
        System.out.println(myApp.identifyMyself());
    }
}
```
控制台输出“I am a horse”。
- **已经被其他候选者重写的方法被忽略。**当超类型共享共同父级时，可能会出现这种情况。
考虑以下接口和类：
```java
public interface Animal {
    default public String identifyMyself() {
        return "I am an animal.";
    }
}
public interface EggLayer extends Animal {
    default public String identifyMyself() {
        return "I am able to lay eggs.";
    }
}

public interface FireBreather extends Animal { }

public class Dragon implements EggLayer, FireBreather {
    public static void main (String... args) {
        Dragon myApp = new Dragon();
        System.out.println(myApp.identifyMyself());
    }
}
```
控制台输出“I am able to lay eggs”

如果两个或多个独立定义的default方法冲突，或default方法与abstract方法冲突，则Java编译器将生成编译器错误。你必须显式重写超类型方法。
考虑以下两个接口OperateCar与FlyCar ，它们有两个相同的方法(startEngine):
```java
public interface OperateCar {
    // ...
    default public int startEngine(EncryptedKey key) {
        // Implementation
    }
}
public interface FlyCar {
    // ...
    default public int startEngine(EncryptedKey key) {
        // Implementation
    }
}
```
一个类同时实现OperateCar和FlyCar必须重写方法startEngine。<font style="color:#ec70ae;">可以使用super关键字调用父类型中任何一个这个default实现。</font>
```java
public class FlyingCar implements OperateCar, FlyCar {
    // ...
    public int startEngine(EncryptedKey key) {
        FlyCar.super.startEngine(key);
        OperateCar.super.startEngine(key);
    }
}
```
super关键字之前的名称（这个例子中是FlyCar或OperateCar）必须引用直接的父接口，该父接口定义或继承了调用的default方法。这种形式的方法调用不限于区分多个包含具有相同签名的default方法的实现的接口。你可以使用super关键字在类和接口中调用default方法。

<font style="color:#ec70ae;">从类继承的实例方法可以重写抽象接口方法。</font>考虑以下接口和类：
```java
public interface Mammal {
    String identifyMyself();
}
public class Horse {
    public String identifyMyself() {
        return "I am a horse.";
    }
}
public class Mustang extends Horse implements Mammal {
    public static void main(String... args) {
        Mustang myApp = new Mustang();
        System.out.println(myApp.identifyMyself());
    }
}
```
控制台输出“I am a horse”。Mustang从Horse继承了identifyMyself方法，该方法重写了在接口Mammal中相同名字的抽象方法。

> **接口中的静态方法从不会被继承**。

#### 修饰符
**重写方法的访问权限可以比被重写的方法更大，但不能更小。**例如一个在父类中protected修饰的实例方法，可以在子类中设置为public，但不能是private。
**此外如果将父类中的实例方法在子类中改为静态方法，会产生编译时期错误，反之亦然。**

#### 总结
下表总结了当使用与超类中的方法相同的签名定义方法时会发生什么。

|  |父类<br/>实例方法|父类<br/>静态方法|
|---|---|---|
|子类<br/>实例方法|重写|编译时期错误|
|子类<br/>静态方法|编译时期错误|隐藏|

> 在一个子类中，可以重载从超类继承的方法。这种重载的方法既不隐藏也不重写超类实例方法 - 它们是子类新的方法。

### 多态（Polymorphism）

在多态的情况下，声明为父类类型的引用变量只能调用父类中的方法，但如果此变量实际引用的是子类对象，而子类对象中重写了父类的方法，这时父类对象调用的是子类中的方法，这种机制就成为**虚方法调用**（virtual method invocation ）。

在Java中，所有非静态方法都是默认的“虚函数（virtual functions.）”。只有标有关键字final（不能被重写）的方法以及不能被继承的private方法都是非虚的。虚方法在编译时期和运行时期被区别对待。JVM专门利用一个虚方法表来用于虚方法分配。

### 隐藏字段
在一个类中，如果有和父类中相同名称的字段，该字段会隐藏父类字段，**即使字段类型并不相同**。子类可以通过super关键字来引用父类被隐藏字段。一般来说，不建议隐藏字段，因为它使代码难以阅读。

### super关键字
super关键字可以在子类中调用父类被隐藏的字段和方法。此外，super还可以调用父类的构造方法，使用super调用父类构造方法时，必须放在子类构造方法方法体第一行。
> 如果构造方法没有显式调用父类构造方法，Java编译器会自动将插入对父类的无参数构造的调用。如果父类没有无参构造，那么会产生一个编译时期错误。Object有这样一个构造函数，所以如果Object是唯一的父类，没有问题。

子类构造方法显式或隐式地调用其父类的构造方法时，会有一整链的构造方法调用，一直返回到Object的构造方法。这被称为构造链。