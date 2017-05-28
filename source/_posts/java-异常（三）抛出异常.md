title: java 异常（三）抛出异常
id: 1495945032365
author: 不识
date: 2017-05-28 12:17:15
tags:
---
# 抛出异常
在我们处理一个异常前，一定是在代码某个部分抛出了一个异常。任何代码都可以引发异常：您的代码，来自其他人（例如Java平台附带的软件包）或Java运行时环境的程序包编写的代码。

Java平台提供了许多异常类。所有类都是Throwable类的后代，并且所有类都允许程序区分在程序执行期间可能发生的各种异常的类型。从Throwable类继承的对象包括直接子类（从Throwable类直接继承的对象）和间接子类（继承自Throwable类的子级或子级的对象）。下图说明了Throwable类及其最重要子类的类层次结构。如你所见，Throwable有两个直接后代：Error和Exception。
![Throwable](/images/java base/Throwable.gif)

## Error
当Java虚拟机中发生动态链接故障或其他硬故障时，虚拟机会抛出Error。简单的程序通常不会捕获或抛出Error。

## Exception
大多数程序抛出并捕获从Exception类派生的对象。Exception表示程序发生了问题，但不是严重的系统问题。你编写的大多数程序都会抛出并捕获Exception而不是Error。

Java平台定义了Exception类的许多子类。这些子类表示可能发生的各种类型的异常。Exception子类可以分成两种，编译时期异常和运行时期异常，运行时期异常用于指示不正确使用API的异常。


在代码区域抛出异常使用的是throw语句，throw语句只需要一个参数：一个throwable对象。Throwable对象是Throwable类的任何子类的实例。下面是throw语句示例
```java
throw someThrowableObject;
```