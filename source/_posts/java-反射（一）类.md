title: java 反射（一）类
id: 1494322864512
author: 不识
tags:
  - java
  - 反射
categories:
  - java 基础
  - java 反射
date: 2017-05-09 17:41:00
---
每个对象不是一个引用类型是一个原始类型，引用类型全部继承自java.lang.Object.类，枚举，数组，和接口都是引用类型，还有一组固定的原始类型：boolean,byte,short,in,long,char,float,和double.引用类型的例子还包括java.lang.String,以及其他所有原始类型的包装器类，比如java.lang.Double等等。     
对于每种类型的对象，JVM都会实例化一个java.lang.Class类型的不可变实例，也就是说所有类型包括原始类型都有一个相应的Class对象，它可以提供了一系列的方法来检查对象运行时期的属性，比如它的成员和类型信息。Class同样可以用来创建一个新的Class和对象.Class是所有java反射API的入口。

<!-- more -->
***
# 获取Class对象
***
对一个类执行反射操作的入口点是首先获得该类的java.lang.Class对象，Class对象的获取有三种方式，有的所有类型都使用，有的只能用于引用类型。

## Object.getClass()
如果我们拥有一个类的实例的话，获取Class最简单的方法就是调用该实例的getClass()方法，该方法是继承自所有引用类型的父类Object,所以该方式只适用于引用类型。如一下例子

```java
Class c1 = "foo".getClass();
Class c2 = System.console().getClass();

enum E { A, B }
Class c3 = A.getClass();

byte[] bytes = new byte[1024];
Class c4 = bytes.getClass();

Set<String> s = new HashSet<String>();
Class c5 = s.getClass();
```
## .class语法
如果我们有一个可用类型，而没有它的实例的话，我们那可以在类型名后添加.class获取其Class对象，这种方式适用于原始类型，如例
```java
boolean b;
Class c1 = b.getClass();   // compile-time error

Class c2 = boolean.class;  // correct

```
>这里boolean.getClass()将会产生一个编译时期异常，因为boolean是原始类型，不能够被引用。而.class语法则可以正确返回一个对应boolean类型的Class。

对于引用类型同样适用

```java
Class c1 = java.io.PrintStream.class;
Class c2 = int[][][].class;
```

## Class.forName()
还可以通过类名来获取Class对象，这需要使用Class的静态方法forName(),这种方式不能用于原始类型，只能用于引用类型。
```java
Class c = Class.forName("java.util.ArrayList");
```
>这里类名必须使用完全限定类名，如果查找不到该类的话，会抛出ClassNotFoundException异常

数组也是引用类型，因此也可以使用这种方式，但是数组类型完全限定名我们可能不知道，这里可以先通过其他方式获得数组的Class对象，再使用Class的getName()方法来查看其完全限定名。

```java
        System.out.println(int[].class.getName());
        System.out.println(int[][].class.getName());
        System.out.println(double[][][].class.getName());
        System.out.println(String[][].class.getName());
```
控制台输出
>[I  
[[I  
[[[D  
[[Ljava.lang.String;  

## 原始类型Class另一种方式
上面方式中我们获取原始类型的Class对象，只能使用.class语法，此外我们还可以通过原始类型的包装类的TYPE字段来获取相应原始类型的Class.比如
```java
Class c = Double.TYPE
```
这里Double.TYPE完全够等同于double.class。
>void同样可以通过其包装类Void.TYPE来获得它的Class对象。

***
# 获取类信息
***

## 类名
获取类名的方式有三种
第一种使用***getName()***方法获取类的完整名称.
```java
System.out.println(int.class.getName());
System.out.println(boolean[].class.getName());
System.out.println(ArrayList.class.getName());
System.out.println(new Object(){}.getClass().getName());
```
控制台输出
>int   
>[Z   
>java.util.ArrayList   
>com.java.reflection.reflectionCase$3   

第二种使用**getCanonicalName()**方法获取该类在Java语言规范里的名称，没有时返回null。

```java
System.out.println(int.class.getCanonicalName());
System.out.println(boolean[].class.getCanonicalName());
System.out.println(ArrayList.class.getCanonicalName());
System.out.println(new Object(){}.getClass().getCanonicalName());
```

控制台输出
>int  
>boolean[]  
>java.util.ArrayList  
>null 
 
第三种使用**getSimpleName()**方法只获取类名，不包含包名。
```java
System.out.println(int.class.getSimpleName());
System.out.println(boolean[].class.getSimpleName());
System.out.println(ArrayList.class.getSimpleName());
System.out.println(new Object(){}.getClass().getSimpleName());
```


控制台输出
>int   
>boolean[]  
>ArrayList  
>

## 包名
在获取类的完全限定名时就已经包含了包名，如果想要只获取包名的话，使用getPackage()方法

```java
Package aPackage = List.class.getPackage(); 
System.out.println(aPackage.getName());
```
控制台输出
>java.util

## 修饰符
一个类可能声明了一个或多个影响其运行时的行为的修饰符。
- 访问权限修饰符：public,protected,private
- 重写要求修饰符：abstract
- 单例限制修饰符：static
- 最终修饰符：final
- 精确浮点修饰符：strictfp
- 注解

并不是所有的修饰符都可以用在任何类上，比如final无法修饰一个接口，abstract也无法用在枚举类上。Java.lang.reflect.Modifier包含了所有可能修饰符的声明，它还包含了一些可用于解码Class.getModifiers()返回的遗嘱修饰符的方法

```java
int modifiers = Set.class.getModifiers(); 
System.out.println(Modifier.toString(modifiers));
```
控制台输出
>public abstract interface

这里Set是一个接口，接口会有默认的修饰符，编译器会给每个接口自动添加public abstract。

## 父类
使用getSuperClass()方法，通过基类的Class对象，我们可以获取父类的Class对象。如果该类没有父类的话，则会返回null,比如Object或接口的Class对象调用getSuperClass.


## 接口
Java中类是可以多实现的，所以一个类的接口可能有多个。使用getInterfaces()方法可以获取一个类的接口Class对象数组，如下
```java
 Class<?>[] interfaces = ArrayList.class.getInterfaces();
        for (Class<?> anInterface : interfaces) {
            System.out.println(anInterface.getName());
        }
```
控制台输出
>java.util.List
>java.util.RandomAccess
>java.lang.Cloneable
>java.io.Serializable


## 注解
使用getAnnotations()方法获取类的所有注解，或者使用getAnnotations(Class)来获取一个指定的注解

```java
        Class c = Identity.class;
        Annotation[] annotations = c.getAnnotations();

        for (Annotation annotation : annotations) {
            System.out.println(annotation.toString());
        }

        System.out.println(c.getAnnotation(Deprecated.class));

```

控制台输出
>@java.lang.Deprecated()  
>@java.lang.Deprecated()

需要注意的是并不是所有的注解都可以通过反射获得，只有该注解对象的标示为Retention(value=RUNTIME)才可以，它表明该注解在运行时期可用。Java中有三个预设注解@Deprecated, @Override, and @SuppressWarnings 其中只有@Deprecated在运行时期是可用的。

# 创建一个新的类实例
有两个可以利用反射创建新的类实例的方法，一个通过Class.newInstance()，一个Constructor.newInstance()，后面一个是构造器对象的方法，在后面类成员的学习里有。通常我们使用后者，他们之间有一下差异。

- Class.newInstance()只能调用无参构造，而Constructor.newInstance()可以调用任何一个构造器，无论是否有参数
- Class.newInstance() 可能会抛出任何异常，无论此异常是否被检查，而 Constructor.newInstance()则会使用InvocationTargetException包装抛出的异常
- Class.newInstance()需要构造器是可见的，而 Constructor.newInstance()在某种环境下可以调用私有构造器。

下面看Class.newInstance()的实际例子
```java
       Class<ArrayList> c1 = ArrayList.class;
        Class<Math> c2 = Math.class;

        ArrayList list = c1.newInstance();  //success
        Math math = c2.newInstance();     //error
```
>java.lang.Math是一个工具类，只有一个私有无参构造