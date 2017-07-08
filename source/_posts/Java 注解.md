title: Java 注解
id: 1499490264823
author: 不识
tags:
  - java
categories:
  - java 基础
  - java 注解
  - ''
date: 2017-07-08 13:04:00
---
[原文链接：](http://docs.oracle.com/javase/tutorial/java/annotations/index.html)注解是元数据的一种形式，它提供了一个不属于程序本身的关于程序的数据。注解对他们注释的代码的操作没有直接的影响。

注解有很多用途，其中：
- **编译器的信息**—注解可以被编译器使用，用来探测错误或者抑制警告。
- **编译时或部署时进行处理**—软件工具可以处理注解信息用来生成代码，XML文件等等。
- **运行时处理**—一些注解可在运行时检查。

本课介绍了可以使用注解的位置，如何应用注解，有哪些预定义注解类型在Java平台标准版（Java SE API）中可用，如何将类型注解与可插拔型系统结合使用，以更强类型检查来编写代码，以及如何实现重复注解。
<!-- more -->
# 注解基础
## 注解的格式
在其最简单的形式中，注解如下所示：
```java
@Entity
```
at符号（@）向编译器指出以下是注解。下面这个示例，注解的名称是Override
```xml
@Override
void mySuperMethod() { ... }
```
注解可以包含已命名或未命名的元素，并且这些元素有值：
```java
@Author(
   name = "Benjamin Franklin",
   date = "3/27/2003"
)
class MyClass() { ... }
or

@SuppressWarnings(value = "unchecked")
void myMethod() { ... }
```
如有只有一个名为value的元素，那么这个名称可以省略，如：
```java
@SuppressWarnings("unchecked")
void myMethod() { ... }
```
如果一个注解没有元素，那么括号可以被省略，如之前的@Override示例。

同样的声明也可以使用多个注解：
```java
@Author(name = "Jane Doe")
@EBook
class MyClass { ... }
```
如果几个注解是相同的类型，那么这个被称作一个重复注解。
```java
@Author(name = "Jane Doe")
@Author(name = "John Smith")
class MyClass { ... }
```
**从Java SE 8版本开始支持重复注解**。有关详细信息，请参阅重复注解。

注解类型可以是Java SE API的java.lang或java.lang.annotation包中定义的类型之一。在前面的示例中，Override和SuppressWarning是Java预定义注解。而前面示例中Author和Ebook是自定义注解。

## 注解可以在哪里使用
注解可以应用于声明：类，字段，方法以及其他程序元素的声明。在声明中使用时，每个注解通常按照惯例出现在它的行上。


从Java SE 8版本开始，注解也可以应用于类型的使用。这里有些例子：
- 类实例创建表达式：
```java
new @Interned MyObject();
```
- 类型转换
```java
  myString = (@NonNull String) str;
```
- implements语句
```java
class UnmodifiableList<T> implements  @Readonly List<@Readonly T> { ... }
```
- 异常抛出声明
```java
void monitorTemperature() throws @Critical TemperatureException { ... }
```

这种形式的注解叫做类型注解。更多信息请参阅[类型注解与可插拔型系统](http://docs.oracle.com/javase/tutorial/java/annotations/type_annotations.html)

# 声明一个注解类型

许多注解取代了代码内的注释。

假设一个软件组传统上在每个类体的开始使用注释来提供一些重要信息。
```java
public class Generation3List extends Generation2List {

   // Author: John Doe
   // Date: 3/17/2002
   // Current revision: 6
   // Last modified: 4/12/2004
   // By: Jane Doe
   // Reviewers: Alice, Bill, Cindy

   // class code goes here

}
```
使用注解来添加这些相同的元数据，你首先必须定义注解类型。这样做的语法是：
```java
@interface ClassPreamble {
   String author();
   String date();
   int currentRevision() default 1;
   String lastModified() default "N/A";
   String lastModifiedBy() default "N/A";
   // Note use of array
   String[] reviewers();
}
```
注解类型定义看起来和接口定义很相似，其中关键字interface前面添加了at符号(@)（@ = AT，作为注释类型）。注解类型是是接口的一种形式。

前面注解体内的定义包含了注解类型元素声明，它们看来了很像方法。注意，它们可以定义一个可选的默认值。

在注解类型被定义后，你可以填充上值使用这个类型的注解，像这样：
```java
@ClassPreamble (
   author = "John Doe",
   date = "3/17/2002",
   currentRevision = 6,
   lastModified = "4/12/2004",
   lastModifiedBy = "Jane Doe",
   // Note array notation
   reviewers = {"Alice", "Bob", "Cindy"}
)
public class Generation3List extends Generation2List {

// class code goes here

}
```
> **注意**：要使@ClassPreamble中的信息显示在Javadoc生成的文档中，您必须使用@Documented注解对@ClassPreamble定义进行注解：
```java
// import this to use @Documented
import java.lang.annotation.*;

@Documented
@interface ClassPreamble {

   // Annotation element definitions
   
}
```
# 预定义注解类型
一组注解释类型是在Java SE API中预定义的。一些注解类型由Java编译器使用，一些注解类型也用于其他注解。

## Java语言使用的注解类型
java.lang中定义的预定义注解类型为@Deprecated，@Override和@SuppressWarnings。


**@Deprecated**注解表示标记的元素已被弃用，不应再使用。无论程序什么时候使用一个被@Deprecated注解的方法，类或者字段，编译器都会产生一个警告。当一个元素已经弃用，，还应使用Javadoc @deprecated标签进行记录，如以下示例所示。在Javadoc注释和注解中使用at符号（@）不是巧合的：它们在概念上是相关的。另外请注意，Javadoc标签以小写d开头，而注解以大写D开头
```java
   // Javadoc comment follows
    /**
     * @deprecated
     * explanation of why it was deprecated
     */
    @Deprecated
    static void deprecatedMethod() { }
}
```

**@Override**注解通知编译器这个元素意图重写在超类中声明的元素。
```java
 // 标记方法作为被重写的超类方法
   @Override 
   int overriddenMethod() { }
```
虽然在重写方法时不需要使用此注解，但它有助于防止错误。如果一个使用@Override标记的方法没有能够正确重写一个它的超类中的方法，编译器会产生一个错误。

**@SuppressWarnings**注解告诉编译器去抑制一个它可能会产出的特定警告。在下面的示例中，使用了一个弃用方法，并且编译器经常会产生一个警告。然而，在这种情况下，这个注解到这警告被抑制。
```java
 //使用弃用方法并且告诉编译器不要产生警告
   @SuppressWarnings("deprecation")
    void useDeprecatedMethod() {
        // deprecation warning
        // - suppressed
        objectOne.deprecatedMethod();
    }
```
每个编译器警告都属于一个类别。Java语言规范列举了两种分类：deprcation和unchecked。在泛型出现之前编写的遗留代码进行接口事，可能会发生unchecked警告。要想抑制多个种类的警告，使用以下语法
```java
@SuppressWarnings({"unchecked","deprecation"})
```
**@SafeVarargs**注解，当应用于一个方法或构造器，断言该代码不会对它的可变参数执行潜在的不安全操作。当这个注解类型被使用，与可变参数相关连的unchecked警告被抑制。

**@FunctionallInterface**注解在Java8中被引入，它表示类型声明旨在作为一个Java语言规范定义的函数式接口。

## 应用于其他注解的注解

一个应用于其他注解的注解被称为元注解（meta-annotations）。这有几个定义在java.lang.annotation中的元注解类型。

**@Retention**注解指示被标记的注解如何储存：
- RetentionPolicy.SOURCE – 被标记注解仅在源代码阶段被保留，并被编译器忽略。
- RetentionPolicy.CLASS – 被标记注解在编译时期由编译器保留，但是被Java虚拟机（JVM）忽略。
- RetentionPolicy.RUNTIME – 被标记注解被JVM保留，所以它可以由运行时环境使用。

**@Documented**注解表示无论什么时候使用被指定注解，都应该使用Javadoc工具记录这些元素。（默认情况下，注解不包括在Javadoc中。）更多信息，参阅[Javadoc工具页面](https://docs.oracle.com/javase/8/docs/technotes/guides/javadoc/index.html)
**@Target**注解表示另一个注解，以限制这个注解可以应用的Java元素类型。一个target注解指定以下元素类型之一作为它的值：
- ElementType.ANNOTATION\_TYPE可以应用于一个注解类型
- ElementType.CONSTRUCTOR可以应用于一个构造器。
- ElementType.FIELD可以应用一个字段或属性。
- ElementType.LOCAL\_VARIABLE可以应用于一个本地变量
- ElementType.METHOD可以应用于一个方法级注解
- ElementType.PACKAGE可以应用于一个包声明
- ElementType.PARAMETER可以应用于方法的参数
- ElementType.TYPE可以应用于任何类元素

**@Inherited**注解表示这个注解类型可以从超类继承（默认下是不可以的）。当用户查询注解类型，而类没有此类型注解时，会在类的超类进行查询。这个注解只能应用于类声明。

**@Repeatable**注解，在JavaSE 8中引入，表示被标记的注解可以被多次应用于相同的声明或类型使用。更多信息请看[重复注解](#重复注解)

# 类型注解和可插拔类型系统
在Java SE 8发布之前，注解只能应用于声明。从Java SE 8版本开始，注解也可以应用于任何的类型使用。这意味着注解可以在你使用类型的任何位置使用。使用类型的几个例子是类实例创建表达式（new），类型转换。implements语句，以及throws语句。这个形式的注解被称为一个类型注解，并且在[注解基础](#注解基础)中提供了几个示例。类型注解被创建是用来支持提升Java语言的分析方式，以确保更强大的类型坚持。JavaSe 8没有提供一个类型检查框架，但是它运行你编写（或下载）一个类型检查框架，该框架实现为与Java编译器结合使用的一个或多个可插拔模块。

比如，你需要确保一个在你的程序中的特定的变量永远不会被赋值为null；因为想要避免一个NullPointerException。您可以编写一个自定义插件来检查。然后你会修改你的代码来注解该变量，指示它永远不会被赋值为null。这个变量声明可能看起来是这个样子：
```java
@NonNull String str；
```
当你编译代码时，将NonNull模块包含在命令行内，如果编译器探测到一个潜在的问题，就会打印一个警告，运行你修改代码来避免这个问题。在你正确修改代码移除所有警告后，这个特定的错误不会在程序运行时发生。

你可以使用多类型检查模块，其中每个模块检查一种类型的错误。以这种方式，你可以在Java类型系统之上构建，在你想要的任何时期或位置添加指定的检查。

在恰当的使用类型注解和可插拔类型检查的存在下，你可以编写更加健壮和更不容易出错的代码。

以在很多情况下，你不需要编写你自己的类型检查模块。这是第三方已经为你做过的工作。比如，例如，您可能希望利用华盛顿大学创建的Checker Framework 。这个框架包含了NonNull模块，以及一个正则表达式模块和一个互斥锁模块。更多信息查看[Checker Framework](http://types.cs.washington.edu/checker-framework/)。

# 重复注解
很多情景下你想要应用相同的注解到一个声明或类型使用上。从Java SE 8版本开始，重复注解使你能够做到这一点。

比如，你正在编写代码使用了一个时间服务，使你可以在给定时间或者在某个时间表上运行一个方法，这类似于UNIX的cron服务。现在你想要设置一个定时器来运行一个方法-doPeriodicCleanup，在这个月的最后一天和每个星期五晚上11点。要设置这个定时器运行，创建一个@Schedule注解并且在doPeriodicCleanup方法上应用两次。第一次使用指定这个月的最后一天，第二次指定周五的下午11点，如下代码所示
```java
@Schedule(dayOfMonth="last")
@Schedule(dayOfWeek="Fri", hour="23")
public void doPeriodicCleanup() { ... }
```
前一个示例应用一个注解到方法上。你可以在使用标准注解的任何地方使用重复注解。比如，比有一个类用来处理未经授权的访问异常。你使用
@Alert注解，一个为manager，另一个为admin来注解这个类：
```java
@Alert(role="Manager")
@Alert(role="Administrator")
public class UnauthorizedAccessException extends SecurityException { ... }
```
出于兼容性原因，重复注解存储在由Java编译器自动生成的容器注解中。为了使编译器执行此操作，您的代码中需要两个声明。
## 步骤1:声明一个重复注解类型
这个注解类型必须使用@Repeatable元注解标记。下面示例定义了一个自定义@Schedule重复注解类型：
```java
import java.lang.annotation.Repeatable;

@Repeatable(Schedules.class)
public @interface Schedule {
  String dayOfMonth() default "first";
  String dayOfWeek() default "Mon";
  int hour() default 12;
}
```
在括号中@Repeatable元注解的值是Java编译器生成的用于存储重复注解的容器注解的类型。在这个示例中，容器注解类型是Schedules，所以重复注解@Schedule被存储在一个@Schedules注解中。

没有声明注解是重复注解就应用相同的该注解到一个声明上会导致编译时期错误。

## 步骤2:声明容器注解类型
容器注解类型必须有一个数组类型的value元素。数组类型的组件类型必须是重复注解类型。Schedules容器注解类型的声明如下所示：
```java
public @interface Schedules {
    Schedule[] value();
}
```
## 检索注解
Reflection API中有几种可用于检索注释的方法。有的方法可以返回单个注解，比如AnnotatedElement.getAnnotationByType(Class&lt;T>),如果一个请求的注解类型存在的话，它们只返回一个单独的注解。如果请求类型的注解超过一个的话，你可以通过首先获取它们的容器注解来获取它们。以这种方式，遗留代码可以继续运行。在Java SE 8中引入了其他方法，扫描容器注解以一次返回多个注解，比如AnnotatedElement.getAnnotations(Class&lt;T>),有关所有可用方法的信息，请参阅AnnotatedElement()类规范。

## 设计注意事项
在设计注解类型时，必须考虑一个注解类型的基数。如果注解的类型被标记为@Repeatable，则可以使用一个注解零次，一次或多次。也可以通过使用@Target元注解来限制注解类型的使用位置。比如你可以创建一个注解，只能用在方法和字段上。重要的是设计注解的时候要仔细确保程序员使用注解时发现它尽可能灵活和强大。

