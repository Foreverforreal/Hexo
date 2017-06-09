title: java 泛型（五）泛型，继承和子类型
id: 1496976381729
author: 不识
date: 2017-06-09 10:46:23
tags:
  - java
  - 泛型
categories:
  - java 基础
  - java 泛型
---



如您所知，如果类型是兼容的，则可以将一种类型的对象分配给另一种类型的对象。例如，您可以分配一个Integer给一个Object，因为Object是Integer的父类型之一：
```java
Object someObject = new Object();
Integer someInteger = new Integer(10);
someObject = someInteger;   // OK
```
在面向对象的术语中，这被称为“是一个（is a）”关系。因为Integer是一种Object，所以这种赋值是允许的。但是Integer同时也是一种Number,所以下面的代码也是有效的：  
```java
public void someMethod(Number n) { /* ... */ }

someMethod(new Integer(10));   // OK
someMethod(new Double(10.1));   // OK
```
泛型同样如此。您可以执行一个泛型类型调用，将Number作为其类型参数传递，并且如果参数与Number兼容，则将允许任何后续的add方法调用：
```java
Box<Number> box = new Box<Number>();
box.add(new Integer(10));   // OK
box.add(new Double(10.1));  // OK
```
现在考虑以下方法：
```java
public void boxTest(Box<Number> n) { /* ... */ }
```

它接受什么类型的论据？通过查看其签名，您可以看到它接受一个类型为Box &lt;Number>的单个参数。但是，这是什么意思？它是否允许按照您的预期传递Box &lt;Integer>或Box &lt;Double>？答案是“否”，因为Box &lt;Integer>和Box &lt;Double>不是Box &lt;Number>的子类型。  
这是在使用泛型编程时一个最常见的误解，但这是一个重要的学习概念
![generics-subtypeRelationship](/images/java base/generics-subtypeRelationship.gif)
>注意：给定两个具体类型A和B（例如Number和Integer），MyClass &lt;A>与MyClass &lt;B>无关，无论A和B是否相关。 MyClass &lt;A>和MyClass &lt;B>的父对象是Object。

# 泛型类和子类型
以Collections类作为示例，ArrayList &lt;E>实现List &lt;E>，并且List &lt;E>扩展Collection &lt;E>。所以ArrayList &lt;String>是List &lt;String>的子类型，它是Collection &lt;String>的子类型。只要不改变类型参数，子类型之间的关系将保留在类型之间。
![generics-sampleHierarchy](/images/java base/generics-sampleHierarchy.gif)

现在想象我们要定义我们自己的List接口PayloadList，它将泛型类型P的可选值与每个元素相关联。其声明可能如下所示：
```java
interface PayloadList<E,P> extends List&lt;E> {
  void setPayload(int index, P val);
  ...
}
```
以下PayloadList参数都是List &lt;String>的子类型：
- PayloadList&lt;String,String>
- PayloadList&lt;String,Integer>
- PayloadList&lt;String,Exception>


![generics-payloadListHierarchy](/images/java base/generics-payloadListHierarchy.gif)
