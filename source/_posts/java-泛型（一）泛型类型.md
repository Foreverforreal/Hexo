title: java 泛型（一）泛型类型
id: 1496821229203
author: 不识
date: 2017-06-07 15:41:05
tags:
  - java
  - 泛型
categories:
  - java 基础
  - java 泛型
---
泛型在定义类，接口，和方法时使类型变得参数化。这很像在方法声明中使用的形式参数，参数化类型为我们提供了在不同的输入参数下重用代码的方式。不同的是，形式参数的输入是值，而类型参数的输入是类型。  
使用泛型的代码比非泛型代码有很多好处：
- 编译时期更加强大的类型检查  
Java编译器对泛型代码提供强类型检查，如果代码违反了类型安全，编译器将报错。修复编译时期错误要比运行时期错误更加容易，因为编译时期错误很难发现。

<!-- more -->
- 消除类型转换  
不使用泛型的代码片段
```java
List list = new ArrayList();
list.add("hello");
String s = (String) list.get(0);
```
	当使用泛型重写时，代码不需要进行类型转换
```java
List<String> list = new ArrayList<String>();
list.add("hello");
String s = list.get(0);   // no cast
```
- 通过使用泛型，程序员可以实现对不同类型的集合进行操作的通用算法，可以自定义，并且类型安全易于阅读

一个泛型类型是一个对类型进行参数化的泛型类或者接口。下面将通过修改以下Box类来演示该概念。
# 一个简单的Box类

首先检查对任意类型的对象进行操作的非泛型Box类。它只需要提供两个方法:向box中添加对象的set方法，以及检索对象的get方法。

```java
public class Box {
    private Object object;

    public void set(Object object) { this.object = object; }
    public Object get() { return object; }
}
```
由于它的方法接受或返回一个Object，所以你可以随意传入任何你想要的传入的，只要它不是一个原始类型。这些方法在编译时期无法验证类的使用方式。在代码的某一部分可能在box中放入了一个Integer，并期望从其中获取Integer，而另一部分代码中可能会错误地传入一个String，从而导致运行时错误。

# 泛型版本的Box类
泛型类使用以下格式定义：  
> class name<T1, T2, ..., Tn> { /\* ... */ }

类型参数部分由尖括号（<>）分隔，跟在类名之后。它指定类型参数（也称为类型变量）T1，T2，...和Tn。

要更新Box类以使用泛型，您可以通过将代码“public class Box”更改为“public class Box &lt;T>”来创建泛型类型声明。这引入了可以在类中任何地方使用的类型变量T。
```java
/**
 * Generic version of the Box class.
 * @param <T> the type of the value being boxed
 */
public class Box<T> {
    // T stands for "Type"
    private T t;

    public void set(T t) { this.t = t; }
    public T get() { return t; }
}


```

您可以看到，所有出现的Object都被替换为T。类型变量可以是您指定的任何非基本类型：任何类类型，任何接口类型，任何数组类型，或甚至其他类型变量。  

# 类型参数命名约定

按照惯例，类型参数名称是单个大写字母。这与您已经知道的变量命名惯例形成鲜明对照，并且有很好的理由：没有这个约定，很难说出类型变量和普通类或接口名称之间的区别。  
最常用的类型参数名称是：

-	E——元素（Java Collections Framework广泛使用）
-	K——键（key）
-	N——数字（Number）
-	T——类型（Type）
-	V——值（Value）
-	S,U,V——第二，三，四类型

您将看到这些名称在整个Java SE API和本课程的其余部分使用。
# 调用和实例化泛型类型

要从你的代码中引用泛型的Box类，你必须执行一个泛型类型的调用，用一些具体的值替换T，比如Integer： 
```java
Box<Integer> integerBox;  
```
您可以将泛型类型调用视为与普通方法调用相似，但不是将参数传递给方法，而是将类型参数（在这种情况下为Integer）传递给Box类本身。  

> Type Parameter 和 Type Argument  
许多开发人员可以互换使用术语Type Parameter和Type Argument，但是这些术语是不一样的。当编写代码时，为了创建要给参数化类型，提供了一个type argument。因此，T在Foo&lt;T>中是一个type parameter而String在Foo&lt;String>中是一个type argument。使用这些术语时，本课程将遵守此定义。

像任何其他变量声明一样，此代码实际上并没有创建一个新的Box对象。它只是声明integerBox会保存一个“Box of Ingteger“的引用，该引用指明如何读取Box &lt;Integer>。  
泛型类型的调用通常称为参数化类型。
要实例化此类，请像往常一样使用new关键字，但在类名和括号之间放置&lt;Integer>：
```java
Box<Integer> integerBox = new Box<Integer>(); 
```

# 菱形符号（The Diamond）

在Java SE 7及更高版本中，只要编译器可以从上下文中确定或推断类型参数，就可以使用一组空的类型参数（<>）替换调用泛型构造函数所需的类型参数。例如，您可以使用以下语句创建Box &lt;Integer>的实例：
```java
Box <Integer> integerBox = new Box <>();
```
有关菱形符号和类型推断的更多信息，请参阅类型推断。

# 多类型参数
如前所述，泛型类可以有多个类型参数。例如，泛型的OrderedPair类，它实现了泛型的Pair接口：
```java
public interface Pair<K, V> {
    public K getKey();
    public V getValue();
}

public class OrderedPair<K, V> implements Pair<K, V> {

    private K key;
    private V value;

    public OrderedPair(K key, V value) {
	this.key = key;
	this.value = value;
    }

    public K getKey()	{ return key; }
    public V getValue() { return value; }
}
```
以下语句创建了OrderedPair类的两个实例化：
```java
Pair<String,Integer> p1 = newOrderedPair<String, Integer>("Even", 8);
Pair<String,String> p2 = new OrderedPair<String, String>("hello", "world");
```
代码中，new OrderedPair&lt;String,Integer>实例化K为一个String,V为一个Integer。因此OrderedPair的构造方法的类型参数分别为String和Integer。由于自动装箱，将String和int传递给该类也是有效的。

如The Diamond所述，由于Java编译器可以从声明OrderedPair &lt;String，Integer>推断出K和V类型，所以可以使用菱形符号来缩短这些语句：
```java
OrderedPair<String, Integer> p1 = new OrderedPair<>("Even", 8);
OrderedPair<String, String>  p2 = new OrderedPair<>("hello", "world");
```
要创建泛型接口，请遵循与创建泛型类相同的规定。

# 参数化类型

您也可以使用参数化类型（即List &lt;String>）来作为类型参数（即K或V）。例如，使用OrderedPair &lt;K，V>示例：
```java
OrderedPair<String, Box<Integer>> p = new OrderedPair<>("primes", new Box<Integer>(...));
```