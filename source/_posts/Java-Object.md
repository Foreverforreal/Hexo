title: Java Object
id: 1503742548510
author: 不识
tags: []
categories:
  - java 基础
  - java 重要类
  - ''
date: 2017-08-26 18:15:00
---

***
# Object作为超类
***

**Object**类在**java.lang**包中，它位于类层次结构树的顶部。每个类都是直接或间接派生自**Object**。你使用或编写的每个子类都继承了**Object**的实例方法。你不需要使用任何这些方法，但如果你使用的话，你可能需要使用特定于你的类的代码来重写它们。从**Object**继承的方法有以下几个：

- protected Object clone() throws CloneNotSupportedException
创建并返回这个对象的副本
- public boolean equals(Object obj)
指示一些其他对象是否等于这个对象。
- protected void finalize() throws Throwable
当垃圾回收确定不再引用这个对象时，由垃圾回收器在对象上调用
- public final Class getClass()
返回对象的运行时期的class。
- public int hashCode()
返回对象的哈希码值。

Object的notify，notifyAll和wait方法都在同步程序中独立运行的线程的活动中发挥作用，有以下五种方法：
- public final void notify()
- public final void notifyAll()
- public final void wait()
- public final void wait(long timeout)
- public final void wait(long timeout, int nanos)

> 注意：这些方法有一些微妙的方面，特别是clone方法

***
# clone()方法
***
如果类或它的一个超类实现了**Cloneable**接口，则可以使用**clone()**方法从现有对象创建副本。要创建克隆，如下：
```java
aCloneableObject.clone();
```
这个方法的**Object**实现检查调用**clone()**的对象是否实现了**Cloneable**接口。如果这个对象没有实现，方法会抛出一个**CloneNotSupportedException**异常。目前，你需要知道**clone()**必须声明为:
```java
protected Object clone() throws CloneNotSupportedException
//或者
public Object clone() throws CloneNotSupportedException
```
如果要编写一个clone()方法来重写Object中的话。

如果调用clone()的对象实现了Cloneable接口，Object的clone()方法实现创建与原始对象相同类的对象，并初始化新对象的成员变量，使其具有与原始对象的相应成员变量相同的值。

使你的类变得可复制的最简单的方法是将**implements Cloneable**添加到你的类的声明中。那么你的对象可以调用**clone()**方法。

对于某些类，Object的clone()方法的默认行为足够了。但是，如果对象包含对外部对象的引用，叫做ObjExternal，则可能需要重写clone()以获取正确的行为。否则一个对象对ObjExternal所做的更改也将对它的克隆对象可见。这意味着原始对象和它的克隆对象不再独立—需要解耦它们，你必须重写clone()以便它可以克隆对象和ObjExternal。然后原始对象引用ObjExternal，克隆对象引用ObjExternal的克隆，以便对象及其克隆对象是真正独立的。

***
# equals()方法
***
**equals()**方法比较两个对象的相等性，如果它们相等，则返回**true**。**Object**类中提供的**equals()**方法中是使用等式操作符（==）来确定两个对象是否相等。对于原始数据类型，这会有正确的结果。然而对于对象，则不会。**Object**提供的**equals()**方法测试对象引用是否相等——即如果所比较的对象是完全相同的对象。

要测试两个对象在等价意义（包含相同的信息）上是否相等，你必须重写**equals()**方法。这有一个重写了**equals**方法的Book类：
```java
public class Book {
    ...
    public boolean equals(Object obj) {
        if (obj instanceof Book)
            return ISBN.equals((Book)obj.getISBN()); 
        else
            return false;
    }
}
```
考虑这个测试Book类的两个实例的相等性的代码：
```java
// Swing Tutorial, 2nd edition
Book firstBook  = new Book("0201914670");
Book secondBook = new Book("0201914670");
if (firstBook.equals(secondBook)) {
    System.out.println("objects are equal");
} else {
    System.out.println("objects are not equal");
}
```
即使firstBook和secondBook引用两个不同的对象，此程序也显示"objects are equal"。它们被认为是相等的，因为所比较的对象包含相同的ISBN号码。

如果等式操作符不适用你的类，你应该始终重写equals()方法。
> 如果你重写了equals()，你也必须重写hashCode()。

***
# finalize()方法
***
**Object**类提供了一个回调方法**finalize()**，当对象变成垃圾时可以调用它。Object的finalize()实现什么也没不做，你可以重写finalize()来执行清理，比如释放资源。

**finalize()**方法可能会被系统自动调用，但是什么时候被调用，是不确定的。因此，你不应该依靠此方法为您进行清理。例如，如果在执行I/O后不关闭代码中的文件描述符，并且期望**finalize()**关闭它们，则可能会用尽文件描述符。

***
# getClass()方法
***
你无法重写**getClass**。

**getClass()**方法返回一个**Class**对象，它有可用于获取有关该类信息的方法，例如获取名称（getSimpleName()），超类（getSuperclass()）以及它实现的接口（getInterfaces()）。例如，以下方法获取并显示对象的类名称：
```java
void printClassName(Object obj) {
    System.out.println("The object's" + " class is " +
        obj.getClass().getSimpleName());
}
```
**Class**类在**java.lang**包中，它有大量的方法（超过50个）。例如，你可以测试以查看类是否是一个注解（isAnnotation()，一个接口(isInterface())，或者是一个枚举(isEnum())。你可以看对象的字段 (getFields()) 或者它的方法(getMethods())，等等。

***
# hashCode()方法
***
**hashCode()**返回的值是对象的哈希码，它是对象的十六进制内存地址。

根据定义，如果两个对象相等，它们的哈希码也必须相等。如果你重写equal()方法，你改变了两个相等的方式，Object的hashCode()实现不再有效。因此，如果您重写equals()方法，还必须覆盖hashCode()方法。否则的话，将违反Object.hashCode()的一般合同，这将阻止你的类与所有基于哈希的集合（包括HashMap，HashSet和Hashtable）结合使用。

***
# toString()方法
***
你应该始终考虑在你的类中重写toString()方法。

Object的toString()方法返回一个对象的String表示，当debugg时这非常有用。一个对象的String表示完全取决于对象，这就是为什么你需要重写你的类中的toString()。

你可以使用toString()和System.out.println()来显示对象的文本表示，例如Book的实例：
```java
System.out.println(firstBook.toString());
```
对于正确重写的toString（）方法，这将打印有用的东西，如下所示：
```
ISBN: 0201914670; The Swing Tutorial; A Guide to Constructing GUIs, 2nd Edition
```








