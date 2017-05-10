title: java 反射（一）
id: 1494322864512
author: 不识
tags:
  - java
  - 反射
categories: []
date: 2017-05-09 17:41:00
---



Java反射使我们可以在运行时期检查类，接口，字段和方法。同样可以使用反射来初始化一个对象，调用方法，获取或设置字段的值。
<!-- more -->

# 获取Class对象
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
如果我们有一个可用类型，而没有它的实例的话，我们那可以在类型名后添加.class获取其Class对象，这种方式也适用于原始类型，如例
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
