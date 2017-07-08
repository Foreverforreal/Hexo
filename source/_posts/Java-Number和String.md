title: Java Number和String
id: 1499510771430
author: 不识
tags:
  - java
categories:
  - java 基础
  - java 重要对象
date: 2017-07-08 18:46:00
---

# Number和String
## Numbers
这一节首先讨论Number类（在java.lang包中）以及其子类。特别地，本节将讨论您将使用这些类的实例而不是原始数据类型的情况。此外，本节还介绍了您可能需要和number一起使用的类，例如格式化或使用数学函数来补充Java语言中内置的运算符。最后，有一个关于自动装箱和拆箱的讨论，这是一个简化代码的编译器功能。
## Strings
String，在Java编程中被广泛使用，它是一序列的字符。在Java编程语言中，字符串是对象。这节描述使用String类来创建和操作字符串。它同时还比较String和StringBuilder类。

# Numbers
本节首先介绍java.lang包中的Number类，它的子类，以及使用这些类的实例而不是基本数字类型的情况。
    
本节还介绍了PrintStream和DecimalFormat类，它们提供了编写格式化数字输出的方法。
     
 最后，讨论java.lang包中的Math类。它包含数学函数可以补充Java语言中内置的操作符。这个类有三角函数，指数函数等等的方法。
## Number类
使用数字时，大部分时间您在代码中使用原始类型。例如：
```java
int i = 500;
float gpa = 3.65f;
byte mask = 0xff;
```
但是，这有一些使用对象取代原始类型的理由，Java平台为每个基本数据类型提供了包装类。这些类在对象中包装原始类型。通过，包装由编译器完成-如果在一个期望对象的地方使用了原始类型，编译器会为你装箱这个原始类型到它的包装类中。类似的，如果当期望原始类型的时候你使用一个number对象，编译器会为你对对象拆箱出原始类型。更多信息，请看[自动装箱拆箱](#自动装箱拆箱)。
所有的数字包装类都是抽象类Number的子类：
![数字对象结构](\images\java base\objects-numberHierarchy.gif)
> **注意:**Number类有四个子类，这里没有讨论。BigDecimal和BigInteger用于高精度计算。AtomicInteger和AtomicLong用于多线程应用程序。

这有三个你可能使用一个Number对象而不是一个原始类型的原因：
1. 作为一个期望对象的方法的参数（通常在操作数字集合时使用）。
2. 作为定义在类中的常量使用，比如MIN\_VALUE和MAX\_VALUE,来提供数据类型的上限和下限。
3. 使用类的方法用于将值与其他原始类型进行转换，与字符串进行转换，与其他系统数字（十进制，八进制，十六进制，二进制）之间进行转换。

下表列出了Number类的所有子类实现的实例方法。

***Number的所有子类实现的方法***

|方法|描述|
|----|----|
|byte byteValue()<br>short shortValue()<br>int intValue()<br>long longValue()<br>float floatValue()<br>double doubleValue() |将此Number对象的值转换为返回的原始数据类型。|
|int compareTo(Byte anotherByte)<br>int compareTo(Double anotherDouble)<br>int compareTo(Float anotherFloat)<br>int compareTo(Integer anotherInteger)<br>int compareTo(Long anotherLong)<br>int compareTo(Short anotherShort)|将此Number对象与参数进行比较。|
|boolean equals(Object obj)|确定此数字对象是否等于参数。如果参数不为null，并且是相同类型和相同数值的对象，那么方法返回true。<br> 对于Java API文档中描述的Double和Float对象，有一些额外的要求。|

每个Number类都包含用于将数字转换为字符串和从数字系统之间转换的其他方法。下面的表格列举了在Integer类中的这些方法。其他Number子类的方法类似。

***Integer类的转换方法***

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

***TestFormat.java中使用的转换符和标志***

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
|123456.789|###,###.###|123,456.789|井号（＃）表示数字，逗号是组分隔符的占位符，期间是小数分隔符的占位符。
|123456.789|###.##|123456.79|该值在小数点右侧有三位数，但模式只有两个数字。format方法通过舍入来处理这个。
|123.78|000000.000|000123.780|该模式指定前置和后置的零，因为使用0字符而不是井号（＃）。
|12345.67|$###,###.###|	$12,345.67|模式中的第一个字符是美元符号（$）。请注意，它紧接在格式化输出中最左边的数字之前。

## 超越基本算数
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

***Math基本方法***

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
***指数和对数方法***

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
***三角方法****

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
你可以使用这些包装类之一：Byte，Double，Float，Integer，Long或Short-来包装一个原始类型数字到一个对象中。如果需要的话，Java编译器自动包装（装箱）原始类型并在必要时再次拆箱。

Number类包含常量和有用的类方法。MIN\_VALUE和MAX\_VALUE常数包含该类型对象可以包含的最小和最大值。 byteValue，shortValue和类似方法将一个数字类型转换为另一个数字类型。valueOf方法将一个字符串转换为一个数字，而toString方法将一个数字转换成一个字符串。

要格式化一个包含数字的字符串用来输出，你可以使用在PrintStream类中的printf()或者format()方法。或者，您可以使用NumberFormat类来使用模式自定义数字格式。

Math类包含用于执行数学函数的各种类方法，包括指数，对数和三角方法。Math还包括基本算术函数，如绝对值和舍入，以及一个用于生成随机数的方法random()。

# Characters
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
***Character类中有用的方法***

|方法|描述|
|----|----|
|boolean isLetter(char ch)<br>boolean isDigit(char ch)|分别确定指定的char值是否是字母或数字。
|boolean isWhitespace(char ch)|	确定指定的char值是否为空格。
|boolean isUpperCase(char ch)<br>boolean isLowerCase(char ch)|分别是确定指定的char值大写还是小写。
|char toUpperCase(char ch)<br>char toLowerCase(char ch)|返回指定字符值的大写或小写形式。
|toString(char ch)|返回一个表示指定字符值的String对象，即一个字符的字符串。

## 转义序列
前面带有反斜杠（\）的字符是一个转义序列，对编译器有特殊的意义。下表显示了Java转义序列：

前面带有反斜杠（\）的字符是一个转义序列，对编译器有特殊的意义。下表显示了Java转义序列：
***转义序列***

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


# Strings
String，在Java编程中广泛使用，它是一序列的字符。在Java编程语言中，字符串是对象。

Java平台提供了String类来创建和操作字符串。
## 创建字符