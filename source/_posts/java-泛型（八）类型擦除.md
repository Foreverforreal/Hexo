title: java 泛型（八）类型擦除
id: 1496979199572
author: 不识
tags:
  - java
  - 泛型
categories:
  - java 基础
  - java 泛型
date: 2017-06-09 11:33:21
---
泛型被引入Java语言，以便在编译时提供更严格的类型检查，并支持通用编程。为了实现泛型，Java编译器将类型擦除应用于：
- 在泛型类中用它的边界替换所有类型参数，或者以Object替代如果类型参数是无界的。因此，编译生成的字节码只包含普通的类，接口和方法。
- 必要时插入类型转换以保护类型安全。
- 生成桥接方法来保留扩展泛型类型中的多态。

类型擦除确保不会为参数化类型而创建新类;因此，泛型不会导致运行时额外的开销。
<!-- more -->
# 泛型类型的擦除
在类型擦除过程中，如果类型参数是有界的，则Java编译器将擦除所有类型参数，并将其替换为其边界第一个类型，如果类型参数为无边界，则该对象将替换为Object。
考虑以下通用类，表示单链表中的节点：
```java
public class Node<T> {

    private T data;
    private Node<T> next;

    public Node(T data, Node<T> next) }
        this.data = data;
        this.next = next;
    }

    public T getData() { return data; }
    // ...
}
```
因为类型参数T是无界的，所以Java编译器用Object替换它：
```java
public class Node {

    private Object data;
    private Node next;

    public Node(Object data, Node next) {
        this.data = data;
        this.next = next;
    }

    public Object getData() { return data; }
    // ...
}
```
在以下示例中，泛型Node类使用有界类型参数：
```java
public class Node<T extends Comparable<T>> {

    private T data;
    private Node<T> next;

    public Node(T data, Node<T> next) {
        this.data = data;
        this.next = next;
    }

    public T getData() { return data; }
    // ...
}
```
Java编译器将有界类型参数T替换为第一个边界类，Comparable：
```java
public class Node {

    private Comparable data;
    private Node next;

    public Node(Comparable data, Node next) {
        this.data = data;
        this.next = next;
    }

    public Comparable getData() { return data; }
    // ...
}
```
# 泛型方法的擦除
Java编译器还会擦除泛型方法参数中的类型参数。考虑以下泛型方法：
```java
// Counts the number of occurrences of elem in anArray.
//
public static <T> int count(T[] anArray, T elem) {
    int cnt = 0;
    for (T e : anArray)
        if (e.equals(elem))
            ++cnt;
        return cnt;
}
```
因为T是无边界的，Java编译器用Object替换它：
```java
public static int count(Object[] anArray, Object elem) {
    int cnt = 0;
    for (Object e : anArray)
        if (e.equals(elem))
            ++cnt;
        return cnt;
}
```
假设定义了以下类：  
class Shape { /* ... */ }  
class Circle extends Shape { /* ... */ }  
class Rectangle extends Shape { /* ... */ }  
您可以编写一个泛型方法来绘制不同的形状：
```java
public static <T extends Shape> void draw(T shape) { /* ... */ }
```
Java编译器将T替换为
```java
public static void draw(Shape shape) { /* ... */ }
```

# 类型擦除和桥接方法的影响
有时类型擦除会导致您可能没有预料到的情况。以下示例显示了这种情况如何发生的。这个示例（在Bridge方法中描述）显示了编译器有时会创建一种称为桥接方法的合成方法，作为类型擦除过程的一部分。  
给出以下两个类：    
```java
public class Node<T> {

    public T data;

    public Node(T data) { this.data = data; }

    public void setData(T data) {
        System.out.println("Node.setData");
        this.data = data;
    }
}

public class MyNode extends Node<Integer> {
    public MyNode(Integer data) { super(data); }

    public void setData(Integer data) {
        System.out.println("MyNode.setData");
        super.setData(data);
    }
}

```
类型擦除后，此代码变为：
```java
MyNode mn = new MyNode(5);
Node n = (MyNode)mn;         // A raw type - compiler throws an unchecked warning
n.setData("Hello");
Integer x = (String)mn.data; // Causes a ClassCastException to be thrown.

```
下面是执行代码时会发生什么：
- n.setData("Hello");导致在MyNode类的对象上执行setData(Object)方法。（MyNode类从Node继承了setData(Object)。）
- 在setData（Object）的方法体中，由n引用的字段的对象被分配为String。
- 可以访问通过mn引用的同一对象的字段数据，并且预期它是一个整数（因为mn是一个MyNode，它是一个Node &lt;Integer>。
- 尝试将一个String分配给Integer会导致Java编译器在分配时插入的转换的ClassCastException。

## 桥接方法
当编译一个类或接口，它是继承自参数化的类或接口时，编译器可能需要创建一个被称为桥接方法的合成方法，作为其类型擦除的一部分。您通常不需要担心桥接方法，但如果出现在堆栈跟踪中，则可能会感到困惑。

类型擦除后，Node和MyNode类变为：
```java
public class Node {

    public Object data;

    public Node(Object data) { this.data = data; }

    public void setData(Object data) {
        System.out.println("Node.setData");
        this.data = data;
    }
}

public class MyNode extends Node {

    public MyNode(Integer data) { super(data); }

    public void setData(Integer data) {
        System.out.println("MyNode.setData");
        super.setData(data);
    }
}

```
在经过类型擦除后，方法签名不匹配。 Node的方法变为setData（Object），而MyNode的方法为setData（Integer）。因此，MyNode setData方法不会覆盖Node 的setData方法。
为了解决这个问题，并在类型擦除后保留泛型类型的多态性，Java编译器会生成一个桥接方法，以确保子类型按预期工作。对于MyNode类，编译器为setData生成以下桥接方法：
```java
class MyNode extends Node {

    // Bridge method generated by the compiler
    //
    public void setData(Object data) {
        setData((Integer) data);
    }

    public void setData(Integer data) {
        System.out.println("MyNode.setData");
        super.setData(data);
    }

    // ...
}

```
正如你所看到的，与Node类的setData方法有相同签名的桥接方法，在类型擦除之后将委托给原始的setData方法。

# 不可具体化类型
类型擦除部分讨论了编译器删除与type parameters 和type arguments相关的信息的过程。类型擦除有与变量参数（也称为可变参数）方法相关，其变量形式参数是不可具体化的类型。

## 不可具体化类型
可具体化类型是一种在整个运行时期它的类型信息都可用的类型。这包括原始类型，非泛型类型，原生类型，以及无边界通配符的调用。
不可具体化类型是其信息会在编译时期被类型擦除去除的类型那个。也就是指未定义为无边界通配符的泛型的调用。一个不可具体化类型它的所有信息在允许时期不是都可使用的。比如不可具体化类型List&lt;String>和List&lt;Number>，在运行时期JVM不能得知他们之间的类型差别。如“泛型的限制”章节所示，有种情况下，不能使用不可具体化类型类型：比如在instanceof表达式中，或者作为数组中的元素时。

## 堆污染

当参数化类型的变量指向的不是一个参数化类型的对象时，会发生堆污染。如果程序执行一些会在编译时产生“unchecked”警告的操作，则会出现这种情况。如果在编译时（在编译时类型检查规则的限制内）或在运行时，如果涉及参数化类型（例如，转换或方法调用）的操作的正确性不可得到验证的话，就会产生一个”unchecked”警告。例如，当混合原生类型和参数化类型时，或者执行未检查的转换时，会发生堆污染。
在正常情况下，当同时编译所有代码时，编译器会发出一个”unchecked”警告，以引起您对潜在的堆污染的注意。如果您单独编译部分代码，则很难发现堆污染的潜在风险。如果确保您的代码编译没有警告，则不会有堆污染的发生。

## 使用不可具体化形式可变参数方法的潜在漏洞
包含可变输入参数的泛型方法可能导致堆污染。
考虑下面的ArrayBuilder类。
```java
public class ArrayBuilder {

  public static <T> void addToList (List<T> listArg, T... elements) {
    for (T x : elements) {
      listArg.add(x);
    }
  }

  public static void faultyMethod(List<String>... l) {
    Object[] objectArray = l;     // Valid
    objectArray[0] = Arrays.asList(42);
    String s = l[0].get(0);       // ClassCastException thrown here
  }
}
```
下面示例中，HeapPollutionExample 使用了 ArrayBuiler 类:
```java
public class HeapPollutionExample {

  public static void main(String[] args) {

    List<String> stringListA = new ArrayList<String>();
    List<String> stringListB = new ArrayList<String>();

    ArrayBuilder.addToList(stringListA, "Seven", "Eight", "Nine");
    ArrayBuilder.addToList(stringListB, "Ten", "Eleven", "Twelve");
    List<List<String>> listOfStringLists =
      new ArrayList<List<String>>();
    ArrayBuilder.addToList(listOfStringLists,
      stringListA, stringListB);

    ArrayBuilder.faultyMethod(Arrays.asList("Hello!"), Arrays.asList("World!"));
  }
}
```

编译时，ArrayBuilder.addToList方法的定义会产生以下警告：
>warning: [varargs] Possible heap pollution from parameterized vararg type T

当编译器遇到一个可变参数方法，它将可变形式参数转换到一个数组中。然而Java编程语言不允许创建参数化类型的数组。在方法arrayBuilder.addToList中，编译器将可变形式参数T…elements转换成一个形式参数T[] elements数组。然而由于类型擦除，编译器将可变形式参数转换为Object[] elements。因此，这里有堆污的可能性。  

以下语句将可变形式参数l分配给Object数组objectArgs：
```java
Object[] objectArray = l;
```
这个声明可能会引起堆污。一个与可变形式参数l的参数化类型不相匹配的值可以被分配给变量objectArray，因此可以分配给l。但是，编译器在此语句中不会生成”unchecked“警告。当编译器将形式参数List &lt;String> ... l转换为形式参数List [] l时，编译器已经生成了一个警告。因此这个声明是有效的;这里变量l是List []类型，而List []是Object []的子类型。  
因此，如果将任何类型的List对象分配给objectArray数组的任何部分，编译器不会发出警告或错误，就如下面语句所展示：
```java
objectArray[0] = Arrays.asList(42);
```
这个语句将一个包含一个Integer对象的List对象分配给了objectArray数组的第一个索引
假如你使用以下语句调用
```java
ArrayBuilder.faultyMethod
ArrayBuilder.faultyMethod(Arrays.asList("Hello!"), Arrays.asList("World!"));
```
在运行时期，以下语句JVM抛出一个ClassCastException异常
```java
// ClassCastException thrown here
String s = l[0].get(0);
```
存储在变量l的第一个索引处的对象是List &lt;Integer>类型，但此语句却期待返回一个List &lt;String>类型的对象。

## 阻止来自使用不可具体化形式参数的可变参数方法的警告
如果你声明一个可变参数方法有一个参数化类型的参数，并且你确保方法体内不会因不正确处理可变形式参数而抛出一个ClassCastException或者其他类似的异常，你可以通过向静态和非构造函数方法声明中添加以下注释，来防止编译器为这些类型的可变参数方法生成的警告：
@SafeVarargs  
@SafeVarargs注释是方法合同的一个文档部分; 此注释声明该方法的实现将不会不正确地处理可变形式参数。  
尽管不太可取，但也可以通过在方法声明中加入以下内容来抑制这种警告：  
@SuppressWarnings({"unchecked", "varargs"})  
但是，这种方法不会抑制从该方法调用处产生的警告。如果您不熟悉@SuppressWarnings语法，请参阅”注释“。