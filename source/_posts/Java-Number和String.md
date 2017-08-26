title: Java Number和String
id: 1499510771430
author: 不识
tags:
  - java
categories:
  - java 基础
  - java 重要类
  - ''
date: 2017-07-08 18:46:00
---
# Number和String
***
## Numbers
这一节首先讨论Number类（在java.lang包中）以及其子类。特别地，本节将讨论您将使用这些类的实例而不是原始数据类型的情况。此外，本节还介绍了您可能需要和number一起使用的类，例如格式化或使用数学函数来补充Java语言中内置的运算符。最后，有一个关于自动装箱和拆箱的讨论，这是一个简化代码的编译器功能。
## Strings
String，在Java编程中被广泛使用，它是一序列的字符。在Java编程语言中，字符串是对象。这节描述使用String类来创建和操作字符串。它同时还比较String和StringBuilder类。
***
# Numbers
***
本节首先介绍java.lang包中的Number类，它的子类，以及使用这些类的实例而不是基本数字类型的情况。
    
本节还介绍了PrintStream和DecimalFormat类，它们提供了编写格式化数字输出的方法。
     
 最后，讨论java.lang包中的Math类。它包含数学函数可以补充Java语言中内置的操作符。这个类有三角函数，指数函数等等的方法。
 
 <!-- more -->
## Number类
使用数字时，大部分时间您在代码中使用原始类型。例如：
```java
int i = 500;
float gpa = 3.65f;
byte mask = 0xff;
```
但是，这有一些使用对象取代原始类型的理由，Java平台为每个基本数据类型提供了包装类。这些类在对象中包装原始类型。通常，包装由编译器完成-如果在一个期望对象的地方使用了原始类型，编译器会为你装箱这个原始类型到它的包装类中。类似的，如果当期望原始类型的时候你使用一个number对象，编译器会为你对对象拆箱出原始类型。更多信息，请看[自动装箱拆箱](#自动装箱拆箱)。
所有的数字包装类都是抽象类Number的子类：
![数字对象结构](\images\java base\objects-numberHierarchy.gif)
> **注意:**Number类有四个子类，这里没有讨论。BigDecimal和BigInteger用于高精度计算。AtomicInteger和AtomicLong用于多线程应用程序。

这有三个你可能使用一个Number对象而不是一个原始类型的原因：
1. 作为一个期望对象的方法的参数（通常在操作数字集合时使用）。
2. 作为定义在类中的常量使用，比如MIN\_VALUE和MAX\_VALUE,来提供数据类型的上限和下限。
3. 使用类的方法用于将值与其他原始类型进行转换，与字符串进行转换，与其他系统数字（十进制，八进制，十六进制，二进制）之间进行转换。

下表列出了Number类的所有子类实现的实例方法。

<center>***Number的所有子类实现的方法***</center>

|方法|描述|
|----|----|
|byte byteValue()<br>short shortValue()<br>int intValue()<br>long longValue()<br>float floatValue()<br>double doubleValue() |将此Number对象的值转换为返回的原始数据类型。|
|int compareTo(Byte anotherByte)<br>int compareTo(Double anotherDouble)<br>int compareTo(Float anotherFloat)<br>int compareTo(Integer anotherInteger)<br>int compareTo(Long anotherLong)<br>int compareTo(Short anotherShort)|将此Number对象与参数进行比较。|
|boolean equals(Object obj)|确定此数字对象是否等于参数。如果参数不为null，并且是相同类型和相同数值的对象，那么方法返回true。<br> 对于Java API文档中描述的Double和Float对象，有一些额外的要求。|

每个Number类都包含用于将数字转换为字符串和从数字系统之间转换的其他方法。下面的表格列举了在Integer类中的这些方法。其他Number子类的方法类似。

<center>***Integer类的转换方法***</center>

|方法|描述|
|----|----|
|static Integer decode(String s)|将String串解码为一个Integer。可以接受十进制，八进制或十六进制数字的字符串表示作为输入。|
|static int parseInt(String s)|返回一个整数（只有十进制）|
|static int parseInt(String s, int radix)|返回一个整数，给出十进制，二进制，八进制或十六进制的字符串表示形式（radix分别等于10，2，8或者16）的数字作为输入|
|String toString()|返回一个表示这个Integer值的String对象|
|static String toString(int i)|返回表示指定整数的String对象。|
|static Integer valueOf(int i)|返回一个保存指定原始类型值的Integer对象|
|static Integer valueOf(String s)|返回一个保存指定字符串表示的值的Integer对象|
|static Integer valueOf(String s, int radix)|返回一个保存指定字符串表示的整数值的Integer对象，以radix的值解析。比如，如果是="333"并且radix="8",方法返回等同于八进制数字333的十进制数字。|

## 格式化数字打印输出
***
之前，您看到使用print和println方法将字符串打印到标准输出（System.out）。因为所有的数字都可以转换为字符串（之后课程会看到），你可以使用这些方法来打印字符串和数字的任意混合。然而，Java编程语言还有其他的方法，允许您在包含数字时对打印输出进行更多的控制。

### 打印和格式化方法
java.io包包含一个PrintStream类，它有两个格式化方法，这样你可以使用它们取代print和println方法。这些方法，format和printf相互等同。你一直使用的熟悉的System.out恰好是一个PrintStream对象，所以你可以在System.out上调用PrintStream方法。因此，您可以在您以前使用print或println的代码中的任何位置使用format 或printf。例如，
```java
System.out.format();
```
这两个java.io.PrintStream方法的语法相同：
```java
public PrintStream format(String format, Object... args)
```
其中format是指定要使用的格式化的字符串，args是要使用该格式打印的变量的列表。一个简单的例子就是
```java
System.out.format("The value of " + "the float variable is " +
     "%f, while the value of the " + "integer variable is %d, " +
     "and the string is %s", floatVar, intVar, stringVar); 
```
第一个参数format是一个格式化字符串，用于指定第二个参数args中的对象如何进行格式化。这个格式化字符串包含纯文本以及格式格式说明符，格式说明符是用来格式化参数将Object...args的特殊的字符（符号Object... args被称为可变参数（varargs），这意味着参数的数量可能会有所不同）。

格式说明符以一个百分号（%）开头，并且以一个转换符结尾。 转换符是一个指示要格式化的参数类型的字符。在百分号（％）和转换符之间，你可以有一个可选的标志和说明符。有许多转换符，标志和说明符，它们在java.util.Formatter中有记录。

这是一个基本示例：
```java
int i = 461012;
System.out.format("The value of i is: %d%n", i);
```
％d指示单个变量是十进制整数。％n是一个不依赖的换行符。输出为：
```
The value of i is: 461012
```
printf和format方法被重载。每个都有一个具有以下语法的版本：
```
public PrintStream format(Locale l, String format, Object... args)
```
要在法语系统中打印数字（使用逗号代替浮点数的英文表示形式的小数位），您可以使用：
```java
System.out.format(Locale.FRANCE,
    "The value of the float " + "variable is %f, while the " +
    "value of the integer variable " + "is %d, and the string is %s%n", 
    floatVar, intVar, stringVar); 
```
### 一个示例
下表列出了示例程序TestFormat.java中使用的一些转换符和标志。

<center>***TestFormat.java中使用的转换符和标志***</center>

|转换符|标志|说明|
|------|----|----|
|d| |一个十进制整数
|f|	|一个float
|n|	|适用于运行应用程序的平台的换行符符。您应该始终使用％n，而不是\ n。
|tB| |一个日期和时间转换 - 月份的区域特定全名。
|td, te| |一个日期和时间转换 - 两位数表示月份的天数。td有需要的话要有一个前置的0。te不需要
|ty, tY| |一个日期和时间转换—ty = 两位数表示的年份, tY =四位数表示的年份
|tl| |一个日期和时间转换—十二小时制的小时数
|tM| |一个日期和时间转换—两位数表示的分钟，需要的话要有前置的0
|tp| |一个日期和时间转换—区域特定的am / pm（小写）。
|tm| |一个日期和时间转换—两位数表示的月份，需要的话要有前置的0。
|tD| |一个日期和时间转换—%tm%td%ty形式的日期
| |08|八个字符宽，需要的话要有前置的0
| |+|包括符号，无论整数还是负数
| |,|	包括特定于语言环境的分组字符。
| |-|左对齐..
| |.3|小数点后三位。
| |10.3|十个字符宽，右对齐，小数点后三位。

以下程序显示了您可以使用格式化的一些格式。输出显示在嵌入式注释中的在双引号内：
```java
import java.util.Calendar;
import java.util.Locale;

public class TestFormat {
    
    public static void main(String[] args) {
      long n = 461012;
      System.out.format("%d%n", n);      //  -->  "461012"
      System.out.format("%08d%n", n);    //  -->  "00461012"
      System.out.format("%+8d%n", n);    //  -->  " +461012"
      System.out.format("%,8d%n", n);    // -->  " 461,012"
      System.out.format("%+,8d%n%n", n); //  -->  "+461,012"
      
      double pi = Math.PI;

      System.out.format("%f%n", pi);       // -->  "3.141593"
      System.out.format("%.3f%n", pi);     // -->  "3.142"
      System.out.format("%10.3f%n", pi);   // -->  "     3.142"
      System.out.format("%-10.3f%n", pi);  // -->  "3.142"
      System.out.format(Locale.FRANCE,
                        "%-10.4f%n%n", pi); // -->  "3,1416"

      Calendar c = Calendar.getInstance();
      System.out.format("%tB %te, %tY%n", c, c, c); // -->  "May 29, 2006"

      System.out.format("%tl:%tM %tp%n", c, c, c);  // -->  "2:34 am"

      System.out.format("%tD%n", c);    // -->  "05/29/06"
    }
}
```
### DecimalFormat类
你可以使用java.text.DecimalFormat类来控制前导和后置零，前缀和后缀的显示，分组（以千位分组）分隔符和小数分隔符。DecimalFormat在数字格式化方面提供了很大的灵活性，但它可能使你的代码更加复杂。

下面的示例通过将模式字符串传递给DecimalFormat构造函数来创建一个DecimalFormat对象myFormat。然后DecimalFormat从NumberFormat继承的format()方法由myFormatter调用，它接受一个double值作为参数，并返回一个以字符串格式化的数字：

这是一个示例程序，说明了DecimalFormat的使用：
```java
import java.text.*;

public class DecimalFormatDemo {

   static public void customFormat(String pattern, double value ) {
      DecimalFormat myFormatter = new DecimalFormat(pattern);
      String output = myFormatter.format(value);
      System.out.println(value + "  " + pattern + "  " + output);
   }

   static public void main(String[] args) {

      customFormat("###,###.###", 123456.789);
      customFormat("###.##", 123456.789);
      customFormat("000000.000", 123.78);
      customFormat("$###,###.###", 12345.67);  
   }
}
```
输出是
```
123456.789  ###,###.###  123,456.789
123456.789  ###.##  123456.79
123.78  000000.000  000123.780
12345.67  $###,###.###  $12,345.67
```

下表说明了每行输出。

|值|模式|输出|说明|
|--|----|----|----|
|123456.789|###,###.###|123,456.789|井号（＃）表示数字，逗号是组分隔符的占位符，句点是小数分隔符的占位符。
|123456.789|###.##|123456.79|该值在小数点右侧有三位数，但模式只有两个数字。format方法通过舍入来处理这个。
|123.78|000000.000|000123.780|该模式指定前置和后置的零，因为使用0字符而不是井号（＃）。
|12345.67|$###,###.###|	$12,345.67|模式中的第一个字符是美元符号（$）。请注意，它紧接在格式化输出中最左边的数字之前。

## 超越基本算数
***
Java编程语言使用它的基本运算符：+，\- ，\*，/和％支持其基本运算。java.lang包中的Math类提供了进行更高级数学计算的方法和常量。
Math类中的方法都是静态的，所以你直接从类中调用它们，如下所示：
```java
Math.cos(angle);
```
> **注意：**使用静态导入的语言特性，你不必在每个数学函数前再些Math：
```java
import static java.lang.Math.*;
```
这允许您通过它们的简单的名称来调用Math类的方法。例如：
```java
cos(angle);
```

### 常量和基本方法
Math类包含两个常量：
- Math.E ，这是自然对数的基础
- Math.PI，这是圆周长与其直径之比。

Math类还有超过40个静态方法。下面表格列举了一些基本方法。

<center>***Math基本方法***</center>

|方法|描述|
|----|----|
|double abs(double d)<br>float abs(float f)<br>int abs(int i)<br>long abs(long lng)|返回参数的绝对值。
|double ceil(double d)|返回大于或等于参数的最小整数。返回double类型
|double floor(double d)|返回小于或等于参数的最大整数。返回double类型
|double rint(double d)|返回与参数最接近的整数。返回double类型
|long round(double d)<br>int round(float f)|返回最接近的long或int，如方法的返回类型所示。
|double min(double arg1, double arg2)<br>float min(float arg1, float arg2)<br>int min(int arg1, int arg2)<br>long min(long arg1, long arg2)<br>|返回两个参数中较小的一个。
|double max(double arg1, double arg2)<br>float max(float arg1, float arg2)<br>int max(int arg1, int arg2)<br>long max(long arg1, long arg2)|返回两个参数中较大的一个。

以下程序BasicMathDemo说明了如何使用以下方法之一：
```java
public class BasicMathDemo {
    public static void main(String[] args) {
        double a = -191.635;
        double b = 43.74;
        int c = 16, d = 45;

        System.out.printf("The absolute value " + "of %.3f is %.3f%n", 
                          a, Math.abs(a));

        System.out.printf("The ceiling of " + "%.2f is %.0f%n", 
                          b, Math.ceil(b));

        System.out.printf("The floor of " + "%.2f is %.0f%n", 
                          b, Math.floor(b));

        System.out.printf("The rint of %.2f " + "is %.0f%n", 
                          b, Math.rint(b));

        System.out.printf("The max of %d and " + "%d is %d%n",
                          c, d, Math.max(c, d));

        System.out.printf("The min of of %d " + "and %d is %d%n",
                          c, d, Math.min(c, d));
    }
}
```
这是程序的输出：
```
The absolute value of -191.635 is 191.635
The ceiling of 43.74 is 44
The floor of 43.74 is 43
The rint of 43.74 is 44
The max of 16 and 45 is 45
The min of 16 and 45 is 16
```
### 指数和对数方法
下表列出了Math类的指数和对数方法。
<center>***指数和对数方法***</center>

|方法|描述|
|----|----|
|double exp(double d)|返回自然对数的基数e，取决于参数的幂。
|double log(double d)|返回参数的自然对数。
|double pow(double base, double exponent)|将第一个参数的值返回到第二个参数的幂。
|double sqrt(double d)|返回参数的平方根。

以下程序ExponentialDemo显示e的值，然后调用上表中列出的任意方法中任意选择的数字：
```java
public class ExponentialDemo {
    public static void main(String[] args) {
        double x = 11.635;
        double y = 2.76;

        System.out.printf("The value of " + "e is %.4f%n",
                          Math.E);

        System.out.printf("exp(%.3f) " + "is %.3f%n",
                          x, Math.exp(x));

        System.out.printf("log(%.3f) is " + "%.3f%n",
                          x, Math.log(x));

        System.out.printf("pow(%.3f, %.3f) " + "is %.3f%n",
                          x, y, Math.pow(x, y));

        System.out.printf("sqrt(%.3f) is " + "%.3f%n",
                          x, Math.sqrt(x));
    }
}
```
以下是运行Exponen时会看到的输出
```
The value of e is 2.7183
exp(11.635) is 112983.831
log(11.635) is 2.454
pow(11.635, 2.760) is 874.008
sqrt(11.635) is 3.411
```
### 三角方法
Math类还提供了一组三角函数，如下表所示。传递到这些方法中的每一个的值是以弧度表示的角度。您可以使用toRadians方法将度数转换为弧度。   
<center>***三角方法****</center>

|方法|描述|
|----|----|
|double sin(double d)|返回指定double值的正弦值。
|double cos(double d)|返回指定double值的余弦值。
|double tan(double d)|返回指定double值的正切值。
|double asin(double d)|返回指定double值的反正弦值。
|double acos(double d)|返回指定double值的反余弦。
|double atan(double d)|返回指定double值的反正切值。
|double atan2(double y, double x)|将直角坐标（x，y）转换为极坐标（r，θ）并返回θ。 
|double toDegrees(double d)<br>double toRadians(double d)|将参数转换为度或弧度。

这里有一个程序TrigonometricDemo，它使用这些方法来计算45度角的各种三角值：
```java
public class TrigonometricDemo {
    public static void main(String[] args) {
        double degrees = 45.0;
        double radians = Math.toRadians(degrees);
        
        System.out.format("The value of pi " + "is %.4f%n",
                           Math.PI);

        System.out.format("The sine of %.1f " + "degrees is %.4f%n",
                          degrees, Math.sin(radians));

        System.out.format("The cosine of %.1f " + "degrees is %.4f%n",
                          degrees, Math.cos(radians));

        System.out.format("The tangent of %.1f " + "degrees is %.4f%n",
                          degrees, Math.tan(radians));

        System.out.format("The arcsine of %.4f " + "is %.4f degrees %n", 
                          Math.sin(radians), 
                          Math.toDegrees(Math.asin(Math.sin(radians))));

        System.out.format("The arccosine of %.4f " + "is %.4f degrees %n", 
                          Math.cos(radians),  
                          Math.toDegrees(Math.acos(Math.cos(radians))));

        System.out.format("The arctangent of %.4f " + "is %.4f degrees %n", 
                          Math.tan(radians), 
                          Math.toDegrees(Math.atan(Math.tan(radians))));
    }
}
```
该程序的输出如下：
```
The value of pi is 3.1416
The sine of 45.0 degrees is 0.7071
The cosine of 45.0 degrees is 0.7071
The tangent of 45.0 degrees is 1.0000
The arcsine of 0.7071 is 45.0000 degrees
The arccosine of 0.7071 is 45.0000 degrees
The arctangent of 1.0000 is 45.0000 degrees
```
### 随机数字
random()方法返回0.0和1.0之间的伪随机选择的数字。范围包括0.0但不是1.0。换句话说：0.0 <= Math.random()<1.0。要获取不同范围的数字，可以对随机方法返回的值执行算术运算。例如，要生成0到9之间的整数，您可以写：
```java
int number = (int)(Math.random() * 10);
```
通过将值乘以10，可能值的范围变为0.0 <=数<10.0。

当你需要生成一个随机数时，使用Math.random可以很好地工作。如果需要生成一系列随机数，则应该创建一个java.util.Random实例并调用该对象的方法来生成数字。

## Number总结
***
你可以使用这些包装类之一：Byte，Double，Float，Integer，Long或Short-来包装一个原始类型数字到一个对象中。如果需要的话，Java编译器自动包装（装箱）原始类型并在必要时再次拆箱。

Number类包含常量和有用的类方法。MIN\_VALUE和MAX\_VALUE常数包含该类型对象可以包含的最小和最大值。 byteValue，shortValue和类似方法将一个数字类型转换为另一个数字类型。valueOf方法将一个字符串转换为一个数字，而toString方法将一个数字转换成一个字符串。

要格式化一个包含数字的字符串用来输出，你可以使用在PrintStream类中的printf()或者format()方法。或者，您可以使用NumberFormat类来使用模式自定义数字格式。

Math类包含用于执行数学函数的各种类方法，包括指数，对数和三角方法。Math还包括基本算术函数，如绝对值和舍入，以及一个用于生成随机数的方法random()。
***
# Characters
***
大多数情况下，如果您使用单个字符值，则将使用原始字符类型。例如：
```java
char ch = 'a'; 
// Unicode for uppercase Greek omega character
char uniChar = '\u03A9';
// an array of chars
char[] charArray = { 'a', 'b', 'c', 'd', 'e' };
```
有时候，当你需要使用一个char作为一个对象，例如，作为一个对象期望的方法参数。Java编程语言为这种需求提供了一个包装类包装char到一个Character对象中。类型为Character的对象包含一个字段，其类型为char。这个[Character](https://docs.oracle.com/javase/8/docs/api/java/lang/Character.html)类还提供了一些用于操作字符的有用的类（即静态）方法。

你可以使用Character的构造方法创建一个Character对象：
```java
Character ch = new Character('a');
```
在某些情况下，Java编译器还将为您创建一个Character对象。例如，如果你传递一个原始类型的char给一个期望对象做参数的方法，则编译器会自动为你将char转换为Character。此特性称为自动装箱或拆箱，如果转换为另一种方式。有关自动装箱和拆箱的更多信息，请参阅[自动装箱和拆箱](http://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)。
> **注意：**Character类是不可变的，因此一旦创建，则不能更改Character对象。

下表列出了Character类中一些最有用的方法，但并不详尽。有关此类中所有方法的完整列表（有超过50个），请参阅java.lang.Character API规范。
<center>***Character类中有用的方法***</center>

|方法|描述|
|----|----|
|boolean isLetter(char ch)<br>boolean isDigit(char ch)|分别确定指定的char值是否是字母或数字。
|boolean isWhitespace(char ch)|	确定指定的char值是否为空格。
|boolean isUpperCase(char ch)<br>boolean isLowerCase(char ch)|分别是确定指定的char值大写还是小写。
|char toUpperCase(char ch)<br>char toLowerCase(char ch)|返回指定字符值的大写或小写形式。
|toString(char ch)|返回一个表示指定字符值的String对象，即一个字符的字符串。

## 转义序列
***
前面带有反斜杠（\）的字符是一个转义序列，对编译器有特殊的意义。下表显示了Java转义序列：

前面带有反斜杠（\）的字符是一个转义序列，对编译器有特殊的意义。下表显示了Java转义序列：
<center>***转义序列***</center>

|方法|描述|
|----|----|
|\t|此时在文本中插入一个tab。
|\b|此时在文本中插入退格。
|\n|此时在文本中插入换行符。
|\r|此时在文本中插入回车符。
|\f|此时在文本中插入换页。
|\'|此时在文本中插入单引号。
|\"|此时在文本中插入双引号。
|\\|此时在文本中插入反斜杠字符。

当打印语句中遇到转义序列时，编译器将相应地进行解释。例如，如果要在引号内加上引号，您必须在内部引号上使用转义序列“\”。要打印该句子    
She said "Hello!" to me.   
你应当写
```java
System.out.println("She said \"Hello!\" to me.");
```

***
# Strings
***
## 基本用法
***
String，在Java编程中广泛使用，它是一序列的字符。在Java编程语言中，字符串是对象。

Java平台提供了String类来创建和操作字符串。
### 创建字符串
创建字符串的最直接方式是写：
```java
String greeting = "Hello World";
```
在这个例子中，“Hello world！”是一个字符串文字 - 代码中的一系列字符，用双引号括起来。每当在代码中遇到字符串文字时，编译器将创建一个带有值的String对象 - 这里是，Hello world！。

与任何其他对象一样，您可以使用new关键字和构造函数来创建String对象。String类有十三个构造函数，允许您使用不同的源（例如字符数组）提供字符串的初始值：
```java
char[] helloArray = { 'h', 'e', 'l', 'l', 'o', '.' };
String helloString = new String(helloArray);
System.out.println(helloString);
```
此代码段的最后一行显示hello。
> **注意：**String类是不可变的，所以一旦创建一个String对象，它就不可再改变。String类有许多方法，其中一些将在下面讨论，似乎可以修改字符串。因为String是不可变的，所以这些方法实际是创建并返回了一个新的字符串，它包含了操作的结果

### 字符串长度

用于获取有关对象的信息的方法称为访问器方法。你可以在String中使用的一个访问器方法是length()方法，它返回包含在String对象中的字符数量。在下面两行代码执行后，len等于17：
```java
String palindrome = "Dot saw I was Tod";
int len = palindrome.length();
```
 palindrome是一个对称的单词或句子- 忽略大小写和标点符号，它的向前和向后的拼写相同的。这是一个简短且低效的方案来反转palindrome字符串。调用String的charAt(i)方法，它返回字符串中的第i个字符，从0开始计数。
 ```java
 public class StringDemo {
    public static void main(String[] args) {
        String palindrome = "Dot saw I was Tod";
        int len = palindrome.length();
        char[] tempCharArray = new char[len];
        char[] charArray = new char[len];
        
        // put original string in an 
        // array of chars
        for (int i = 0; i < len; i++) {
            tempCharArray[i] = 
                palindrome.charAt(i);
        } 
        
        // reverse array of chars
        for (int j = 0; j < len; j++) {
            charArray[j] =
                tempCharArray[len - 1 - j];
        }
        
        String reversePalindrome =
            new String(charArray);
        System.out.println(reversePalindrome);
    }
}
```
运行程序生成此输出：
```
doT saw I was toD
```
要完成字符串反转，程序必须将字符串转换为字符数组（第一个for循环），将数组转换为第二个数组（第二个循环），然后转换回一个字符串。String类包含一个方法getChars（），将字符串或字符串的一部分转换为字符数组，所以我们可以用以下代码替换上述程序中的第一个for循环：
```java
palindrome.getChars(0, len, tempCharArray, 0);
```
### 连接字符串
String类包含一个方法用于连接两个字符串：
```java
string1.concat(string2);
```
这个返回一个新的字符串，它是在string1后添加了string2。

你也以字符串文字使用concat()方法，如：
```java
"My name is ".concat("Rumplestiltskin");
```
String更通常的是使用+操作符来连接，比如
```java
"Hello," + " world" + "!"
```
结果是
```
"Hello, world!"
```
+操作符广泛应用于print语句中，比如
```java
String string1 = "saw I was ";
System.out.println("Dot " + string1 + "Tod");
```
打印出
```
Dot saw I was Tod
```
这样的连接可以是任何对象的混合物。对于不是String的每个对象，调用其toString（）方法将其转换为String。
> **注意：**Java编程语言不允许文字字符串跨越源文件中的行，因此您必须在多行字符串中的每一行的末尾使用+连接运算符。例如：
```java
String quote = 
    "Now is the time for all good " +
    "men to come to the aid of their country.";
```
再次的，中断的字符串在行之间使用+字符串，在print语句中非常常见。

### 创建格式化字符串
你已经看到了使用printf()和format()方法来打印输出格式化的数字。String类有一个等同的类方法，format(),它返回一个String对象而不是PrintStream对象。

使用String的静态format()方法运行你创建一个可重用的格式化字符串，而不是一次性打印语句。比如，相比
```java
System.out.printf("The value of the float " +
                  "variable is %f, while " +
                  "the value of the " + 
                  "integer variable is %d, " +
                  "and the string is %s", 
                  floatVar, intVar, stringVar); 
```
你可以这么些
```java
String fs;
fs = String.format("The value of the float " +
                   "variable is %f, while " +
                   "the value of the " + 
                   "integer variable is %d, " +
                   " and the string is %s",
                   floatVar, intVar, stringVar);
System.out.println(fs);
```
## Number与String之间的转换
***
### String转换为Number
通常，一个程序以在字符串对象中的数字数据介绍——比如，用户输入的值。

Number子类包装了原始的数字类型（Byte，Integer，Double，Float，Long和Short）每一个都提供了一个名为valueOf的类方法，它将字符串转换一个那个包装类类型。这有一个例子，ValueOfDemo，它从命令行获取两个字符串，将它们转换为数字，并且在值上执行算数操作：
```java
public class ValueOfDemo {
    public static void main(String[] args) {

        // 这个程序在命令行上需要两个参数
        if (args.length == 2) {
            //字符串转换为数字
            float a = (Float.valueOf(args[0])).floatValue(); 
            float b = (Float.valueOf(args[1])).floatValue();

            // 做一些算数
            System.out.println("a + b = " +
                               (a + b));
            System.out.println("a - b = " +
                               (a - b));
            System.out.println("a * b = " +
                               (a * b));
            System.out.println("a / b = " +
                               (a / b));
            System.out.println("a % b = " +
                               (a % b));
        } else {
            System.out.println("This program " +
                "requires two command-line arguments.");
        }
    }
}
```
以下是使用4.5和87.2作为命令行参数时程序的输出：
```
a + b = 91.7
a - b = -82.7
a * b = 392.4
a / b = 0.0516055
a % b = 4.5
```
> **注意：**每一个Number子类包装了原始数字类型并且提供了一个parseXXX()方法（比如，parseFloat()），它可以用来将字符串转换为yg原始类型数字。因为返回的是一个原始类型而不是对象，parseFloat()方法要比valueOf()方法更直接。比如在ValueOfDemo程序中，我们会使用：
```java
float a = Float.parseFloat(args[0]);
float b = Float.parseFloat(args[1]);
```

### 数字转换为字符串
有时您需要将一个数字转换为字符串，因为您需要以字符串形式对该值进行操作。将数字转换为字符串有几种简单的方法：
```java
int i;
// Concatenate "i" with an empty string; conversion is handled for you.
String s1 = "" + i;
or

// The valueOf class method.
String s2 = String.valueOf(i);
```
每一个Number子类都包含一个类方法，toString
，它将其原始类型转换为字符串。例如：
```java
int i;
double d;
String s3 = Integer.toString(i); 
String s4 = Double.toString(d); 
```
ToStringDemo示例使用toString方法将数字转换为字符串。然后，程序使用一些字符串方法来计算小数点前后的位数：
```java
public class ToStringDemo {
    
    public static void main(String[] args) {
        double d = 858.48;
        String s = Double.toString(d);
        
        int dot = s.indexOf('.');
        
        System.out.println(dot + " digits " +
            "before decimal point.");
        System.out.println( (s.length() - dot - 1) +
            " digits after decimal point.");
    }
}
```
程序输出是
```
3 digits before decimal point.
2 digits after decimal point.
```
## 操纵字符串中的字符
***
String类有许多方法用于检查字符串的内容，查找字符串中的字符或子字符串，更改大小写和其他任务。
### 通过索引获取字符和子字符串
您可以通过调用charAt()访问器方法来获取字符串中特定索引处的字符。第一个字符的索引为0，而最后一个字符的索引为length() - 1。例如，以下代码在字符串中的索引9处获取字符：
```java
String anotherPalindrome = "Niagara. O roar again!"; 
char aChar = anotherPalindrome.charAt(9);
```
指数从0开始，因此索引9处的字符为“O”。

如果要从字符串中获取多个连续字符，可以使用substring方法。子串方法有两个版本，如下表所示：

|方法|描述|
|----|----|
|String substring(int beginIndex, int endIndex)|返回一个新字符串，该字符串是此字符串的子字符串。子字符串从指定的beginIndex开始，并扩展到索引endIndex - 1处的字符。
|String substring(int beginIndex)|返回一个新字符串，该字符串是此字符串的子字符串。 int参数指定第一个字符的索引。这里，返回的子字符串扩展到原始字符串的末尾。
### 操作字符串的其他方法
这里有几个其他用于操作字符串的String方法：

|方法|描述|
|----|----|
|String[] split(String regex)<br>String[] split(String regex, int limit)|搜索由string参数（包含正则表达式）指定的匹配项，并相应地将此字符串拆分为字符串数组。可选的int参数指定返回的数组的最大大小。
|CharSequence subSequence(int beginIndex, int endIndex)|返回从beginIndex索引建立的新字符序列，直到endIndex - 1。
|String trim()|返回此字符串的拷贝，并删除前导和尾随的空格。
|String toLowerCase()<br>String toUpperCase()|返回一个转换为小写或大写的字符串的拷贝。如果不需要转换，这些方法返回原始字符串。
### 搜索字符串中的字符和子字符串
这里有一些其他String方法用于查找字符串中的字符或子字符串。String类提供访问器方法，返回特定字符或子字符串的字符串中的位置：: indexOf()和lastIndexOf().indexOf()方法从字符串的开头向后搜索，lastIndexOf()方法从字符串的末尾向前搜索。如果没有找到一个字符或子字符串，则indexOf()和lastIndexOf()返回-1。

String类还提供了一个搜索方法contains，如果字符串包含特定的字符序列，则返回true。当您只需要知道该字符串包含一个字符序列，但精确位置并不重要时，请使用此方法。

|方法|描述|
|----|----|
|int indexOf(int ch)<br>int lastIndexOf(int ch)|返回指定字符第一次（最后）出现的索引
|int indexOf(int ch, int fromIndex)<br>int lastIndexOf(int ch, int fromIndex)|	从指定索引向后（向前）查找起，返回指定字符第一次（最后一次）出现的索引
|int indexOf(String str)<br>int lastIndexOf(String str)	|返回指定子字符串第一次（最后）出现的索引
|int indexOf(String str, int fromIndex)<br>int lastIndexOf(String str, int fromIndex)|	从指定索引向后（向前）查找起，返回指定子字符串第一次（最后一次）出现的索引
|boolean contains(CharSequence s)|	如果字符串包含指定的字符序列，则返回true。
> **注意：**CharSequence是由String类实现的接口。因此，您可以使用一个字符串作为contains()方法的参数。

### 将字符和子字符串替换为字符串
String类在字符串中插入字符或子字符串的方法很少。一般来说，它们不是必需的：您可以通过将您从字符串删除后剩下的子字符串与要插入的子字符串串联来创建一个新字符串。

然而，String类确实有四种替换找到的字符或子字符串的方法。他们是：

|方法|描述|
|----|----|
|String replace(char oldChar, char newChar)|返回使用newChar替换此字符串中所有出现oldChar的新字符串。
|String replace(CharSequence target, CharSequence replacement)|	将与字面目标序列匹配的字符串的每个子字符串替换为指定的文字替换序列。
|String replaceAll(String regex, String replacement)|替换每个与给定正则表达式匹配的子字符串为给定的替换字符串
|String replaceFirst(String regex, String replacement)|替换第一个与给定正则表达式匹配的子字符串为给定的替换字符串

### 一个示例
以下类Filename说明了使用lastIndexOf()和substring()来隔离文件名的不同部分。
> **注意：**以下Filename类中的方法不执行任何错误检查，并假定它们的参数包含完整的目录路径和带扩展名的文件名。如果这些方法是生产代码，他们将验证他们的参数是否正确构造。

```java
public class Filename {
    private String fullPath;
    private char pathSeparator, 
                 extensionSeparator;

    public Filename(String str, char sep, char ext) {
        fullPath = str;
        pathSeparator = sep;
        extensionSeparator = ext;
    }

    public String extension() {
        int dot = fullPath.lastIndexOf(extensionSeparator);
        return fullPath.substring(dot + 1);
    }

    // gets filename without extension
    public String filename() {
        int dot = fullPath.lastIndexOf(extensionSeparator);
        int sep = fullPath.lastIndexOf(pathSeparator);
        return fullPath.substring(sep + 1, dot);
    }

    public String path() {
        int sep = fullPath.lastIndexOf(pathSeparator);
        return fullPath.substring(0, sep);
    }
}
```
这有一个程序FilenameDemo，它构造一个Filename对象并调用其所有方法：

```java
public class FilenameDemo {
    public static void main(String[] args) {
        final String FPATH = "/home/user/index.html";
        Filename myHomePage = new Filename(FPATH, '/', '.');
        System.out.println("Extension = " + myHomePage.extension());
        System.out.println("Filename = " + myHomePage.filename());
        System.out.println("Path = " + myHomePage.path());
    }
}
```
下面是程序的输出
```
Extension = html
Filename = index
Path = /home/user
```
如下图所示，我们的 extension方法使用lastIndexOf来查找文件名中的句点（.）的最后一次出现。然后substring使用lastIndexOf的返回值来提取文件扩展名 - 即从字符串的句点到结尾的子字符串。该代码假定文件名中有一个句点;如果文件名没有句点，则lastIndexOf返回-1，而substring方法会引发StringIndexOutOfBoundsException。
![](/images/java base/objects-lastIndexOf.gif)
另外，请注意，extension方法使用dot + 1 作为substring的参数。如果期间句点（.）是字符串的最后一个字符，则dot + 1等于字符串的长度，该字符串的长度大于字符串中最大的索引（因为索引从0开始）。这是substring的合法参数，因为该方法接受一个等于但不大于字符串长度的索引，并将其解释为“字符串的结尾”。

## 比较字符串与字符串的部分
***
String类有许多用于比较字符串和字符串部分的方法。下表列出了这些方法。

|方法|描述|
|----|----|
|boolean endsWith(String suffix)<br>boolean startsWith(String prefix)|	如果这个字符串以作为方法参数的指定的字符串开始或结尾，则返回true
|boolean startsWith(String prefix, int offset)|考虑从索引offset开始的字符串，如果以指定为参数的子字符串开头，则返回true。
|int compareTo(String anotherString)|按字典顺序比较两个字符串。返回一个整数，表示该字符串是否大于（result is> 0），等于（result is = 0）或小于（result is <0）。
|int compareToIgnoreCase(String str)|	按字典顺序比较两个字符串，但不考虑大小写。返回一个整数，表示该字符串是否大于（result is> 0），等于（result is = 0）或小于（result is <0）。
|boolean equals(Object anObject)|当且仅当参数是与该对象相同的字符序列的String对象时，才返回true。
|boolean equalsIgnoreCase(String anotherString)|不考虑大小写，当且仅当参数是与该对象相同的字符序列的String对象时，才返回true。
|boolean regionMatches(int toffset, String other, int ooffset, int len)|测试此字符串的指定区域是否与String参数的指定区域匹配。<br>区域的长度为len，并从该字符串的索引toffset开始，从另一个字符串的ooffset开始。
|boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len)	|测试此字符串的指定区域是否与String参数的指定区域匹配。<br>区域的长度为len，并从该字符串的索引toffset开始，从另一个字符串的ooffset开始。<br>布尔值表示是否应忽略大小写;如果为true，则在比较字符时忽略大小写。
|boolean matches(String regex)|测试此字符串是否与指定的正则表达式匹配。

以下程序RegionMatchesDemo使用regionMatches方法来搜索另一个字符串中的字符串：

```java
public class RegionMatchesDemo {
    public static void main(String[] args) {
        String searchMe = "Green Eggs and Ham";
        String findMe = "Eggs";
        int searchMeLength = searchMe.length();
        int findMeLength = findMe.length();
        boolean foundIt = false;
        for (int i = 0; 
             i <= (searchMeLength - findMeLength);
             i++) {
           if (searchMe.regionMatches(i, findMe, 0, findMeLength)) {
              foundIt = true;
              System.out.println(searchMe.substring(i, i + findMeLength));
              break;
           }
        }
        if (!foundIt)
            System.out.println("No match found.");
    }
}
```

程序输出的结果是Eggs。
该程序一步一步地遍历searchMe一个字符引用的字符串。对于每个字符，程序调用regionMatches方法来确定以当前字符开头的子字符串是否与程序正在查找的字符串匹配。

## StringBuilder类

StringBuilder对象就像String对象，除了它可以被修改。在内部，这些对象被视为包含一系列字符的可变长度数组。在任何时候，序列的长度和内容可以通过方法调用来改变。

应该始终使用String，除非StringBuilder在简单代码方面提供了优势（请参阅本节末尾的示例程序）或更好的性能。例如，如果您需要连接大量的字符串，则附加到StringBuilder对象更有效。
### 长度和容量
StringBuilder类就像String类一样有一个length()方法，它返回构建器中字符序列的长度。

不像String，每个StringBuilder都有一个容量，它是已分配的字符空间数。由capacity()方法返回的容量始终大于或等于字符长度（通常大于），并根据需要自动扩展以适应字符串构建器的添加。

<center>***StringBuilder构造器***</center>

|构造器|描述|
|------|----|
|StringBuilder()|创建一个空的字符串构建器，容量为16（16个空元素）。
|StringBuilder(CharSequence cs)|构造一个包含与指定的CharSequence相同的字符的字符串构建器，再在CharSequence尾部加上16个空的元素。
|StringBuilder(int initCapacity)|创建具有指定初始容量的空的字符串构建器。
|StringBuilder(String s)|创建一个字符串构建器，其值由指定的字符串初始化，另外还有一个额外的16个空的元素。

例如，以下代码
```java
//创建空的构建器，容量为16 
StringBuilder sb = new StringBuilder（）;
//开始添加9个字符串 
sb.append（ “问候”）;
```
将生成长度为9的字符串构建器，其容量为16：
![](/images/java base/objects-stringBuffer.gif)
StringBuilder类有一些和长度与容量相关的方法，而在String类中没有的

|构造器|描述|
|------|----|
|void setLength(int newLength)|设置字符序列的长度。如果newLength比length()小，字符序列中最后的字符会被截去。如果newLength比length()大，null字符会被添加到字符序列尾部|
|void ensureCapacity(int minCapacity)|确保容量至少等于指定的最小值。|

许多操作（例如，append()，insert()或setLength()）可以增加字符串构建器中字符序列的长度，以使结果的length()大于当前的capacity()。发生这种情况时，容量会自动增加。

### StringBuilder操作
StringBuilder中不能在String中使用的主要操作是append()和insert()方法，它们被重载以便接受任何类型的数据。每个将其参数转换为字符串，然后将该字符串的字符附加或插入字符串构建器中的字符序列。append方法总是在现有字符序列的末尾添加这些字符，而insert方法会在指定的点添加字符。   
以下是StringBuilder类的一些方法。

|构造器|描述|
|------|----|
|StringBuilder append(boolean b)<br>StringBuilder append(char c)<br>StringBuilder append(char[] str)<br>StringBuilder append(char[] str, int offset, int len)<br>StringBuilder append(double d)<br>StringBuilder append(float f)<br>StringBuilder append(int i)<br>StringBuilder append(long lng)<br>StringBuilder append(Object obj)<br>StringBuilder append(String s)|将参数附加到此字符串构建器。在附加操作发生之前，数据将转换为字符串。|
|StringBuilder delete(int start, int end<br>StringBuilder deleteCharAt(int index)|第一个方法删除子StringBuilder字符序列中从start到end-1（包括）的序列。第二个方法删除位于索引处的字符|
|StringBuilder insert(int offset, boolean b)<br>StringBuilder insert(int offset, char c)<br>StringBuilder insert(int offset, char[] str)<br>StringBuilder insert(int index, char[] str, int offset, int len)<br>StringBuilder insert(int offset, double d)<br>StringBuilder insert(int offset, float f)<br>StringBuilder insert(int offset, int i)<br>StringBuilder insert(int offset, long lng)<br>StringBuilder insert(int offset, Object obj)<br>StringBuilder insert(int offset, String s)|插入第二个参数到字符串构建器。第一个int参数表示要插入数据前的索引。这个数据在插入操作发生前先转换为字符串类型|
|StringBuilder replace(int start, int end, String s)<br>void setCharAt(int index, char c)|替换此字符串构建器中指定的字符。|
|StringBuilder reverse()|反转此字符串构建器中的字符序列。|
|String toString()|返回一个包含构建器中字符序列的字符串。|

> **注意：**您可以在StringBuilder对象中使用String的任何方法，通过使用StringBuilder类的toString()方法将字符串构建器首先转换为一个字符串。然后使用StringBuilder（String str）构造函数将字符串转换回到字符串构建器。

### 一个示例

在“Strings”一节中列出的StringDemo程序是一个程序的例子，如果使用StringBuilder而不是String，程序将会更有效。   
StringDemo反转了一个palindrome。在这里，再次演示：
```java
public class StringDemo {
    public static void main(String[] args) {
        String palindrome = "Dot saw I was Tod";
        int len = palindrome.length();
        char[] tempCharArray = new char[len];
        char[] charArray = new char[len];
        
        // put original string in an 
        // array of chars
        for (int i = 0; i < len; i++) {
            tempCharArray[i] = 
                palindrome.charAt(i);
        } 
        
        // reverse array of chars
        for (int j = 0; j < len; j++) {
            charArray[j] =
                tempCharArray[len - 1 - j];
        }
        
        String reversePalindrome =
            new String(charArray);
        System.out.println(reversePalindrome);
    }
}
```
运行程序生成此输出：
```
doT saw I was toD
```
要完成字符串反转，程序必须将字符串转换为字符数组（第一个for循环），将数组转换为第二个数组（第二个循环），然后转换回一个字符串。

如果将palindrome 字符串转换为字符串构建器，则可以在StringBuilder类中使用reverse（）方法。它使代码更简单，更容易阅读：
```java
public class StringBuilderDemo {
    public static void main(String[] args) {
        String palindrome = "Dot saw I was Tod";
         
        StringBuilder sb = new StringBuilder(palindrome);
        
        sb.reverse();  // reverse it
        
        System.out.println(sb);
    }
}
```
运行此程序生成相同的输出：
```
doT saw I was toD
```
请注意，println（）会打印一个字符串构建器，如：
```java
System.out.println(sb);
```
因为sb.toString()被隐式调用，因为它与println()调用中的任何其他对象一样。
> **注意**还有一个StringBuffer类与StringBuilder类完全相同，除了它通过使其方法同步来线程安全。







