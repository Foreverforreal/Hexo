title: java 异常（二）抛出异常
id: 1495945032365
author: 不识
date: 2017-05-28 10:40:00
tags:
  - java
  - exception
categories:
  - java 基础
  - java 异常
---
在我们处理一个异常前，一定是在代码某个部分抛出了一个异常。任何代码都可以引发异常：您的代码，来自其他人（例如Java平台附带的软件包）或Java运行时环境的程序包编写的代码。无论什么要抛出什么异常，都要使用throw语句来抛出。

# throw语句

所有方法都使用throw语句抛出异常。 throw语句需要一个单个参数：一个可抛出的对象（a throwable object）。可抛出对象是任何一个Throwable类的子类的实例。throw语句写法如下。
>**throw** someThrowableObject

在使用throw语句抛出异常后，程序会在抛出语句后立即终止，它后面的语句将会执行不到。如果抛出异常是检查型对象的话，任何调用该方法的方法都必须对此抛出的异常进行处理，要么使用catch进行捕获，要么使用throws进一步将此异常在调用栈上传播。


# 异常对象
Java平台提供了许多异常类。所有类都是Throwable类的后代，并且所有类都允许程序区分在程序执行期间可能发生的各种异常的类型。从Throwable类继承的对象包括直接子类（从Throwable类直接继承的对象）和间接子类（继承自Throwable类的子级或子级的对象）。下图说明了Throwable类及其最重要子类的类层次结构。如你所见，Throwable有两个直接后代：Error和Exception。
![Throwable](/images/java base/Throwable.gif)

## Error
当Java虚拟机中发生动态链接故障或其他硬故障时，虚拟机会抛出Error。简单的程序通常不会捕获或抛出Error。

## Exception
大多数程序抛出并捕获从Exception类派生的对象。Exception表示程序发生了问题，但不是严重的系统问题。你编写的大多数程序都会抛出并捕获Exception而不是Error。

Java平台定义了Exception类的许多子类。这些子类表示可能发生的各种类型的异常。Exception子类有两种，一种直接继承实现Excetion，一种实现Exception的子类RuntimeException.