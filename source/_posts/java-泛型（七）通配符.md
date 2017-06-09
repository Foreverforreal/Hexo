title: java 泛型（七）通配符
id: 1496977772390
author: 不识
date: 2017-06-09 11:09:33
tags:
  - java
  - 泛型
categories:
  - java 基础
  - java 泛型
---


在泛型代码中，问号（？）被称为通配符，表示一个未知类型。通配符可以用在多种情景下：作为参数的类型，字段，或者局部变量；有时也作为返回类型（尽管具体指定类型会是更好的编程实践）。通配符从不被用作泛型方法调用类型参数，泛型类实例创建或超类型。
# 上边界通配符
您可以使用上边界通配符来放宽对变量的限制。例如，假设你想编写一个适用于List &lt;Integer>，List &lt;Double>和List &lt;Number>的方法;您可以通过使用上限通配符来实现此目的。  
要声明一个上限通配符，请使用通配符（'？'），后跟extends关键字，再后跟其上边界。请注意，在这种情况下，extends在一般意义上用于表示“extends”（如在类中）或“implements”（如在接口中）。   
要写一个适用于Number以及它的子类型，比如Integer，Double和Float的list的方法，应当指定 List<? extends Number>。List &lt;Number>一词比List &lt;？extends Number>限制更大，因为前者匹配一个类型为Number的list，而后者匹配一个类型为Number或其任何子类的list。  
考虑下面的process方法  
```java
public static void process(List<? extends Foo> list) { /* ... */ }  
```
上边界通配符<? extends Foo>中，其中Foo是任何类型，匹配Foo和任何Foo的子类。Process方法可以以Foo类型来访问集合元素
```java
public static void process(List<? extends Foo> list) {
    for (Foo elem : list) {
        // ...
    }
}
```
在foreach子句中，elem变量遍历列表中的每个元素。 Foo类中定义的任何方法现在可以在elem上使用。   
sumOfList方法返回列表中的数字之和：  
```java
public static double sumOfList(List<? extends Number> list) {
    double s = 0.0;
    for (Number n : list)
        s += n.doubleValue();
    return s;
}
```

# 无边界通配符
无边界通配符类型是通过通配符（？）来指定。比如List<？>。这个被称为未知类型list。只有在以下两种情况下，无界通配符是才是有用的：
- 如果您正在编写可以使用Object类中提供的功能实现的方法。
- 当代码使用泛型类中不依赖类型参数的方法时。比如List.size或者List.clear。事实上，Class <？>是经常使用的，因为Class &lt;T>中的大多数方法都不依赖于T.

考虑下面的方法printList
```java
public static void printList(List<Object> list) {
    for (Object elem : list)
        System.out.println(elem + " ");
    System.out.println();
}
```
printList的目标是打印任何类型的list，但是它无法实现该目的 - 它仅打印一个Object实例list;它不能打印List &lt;Integer>，List &lt;String>，List &lt;Double>等，因为它们不是List &lt;Object>的子类型。要编写一个泛型的printList方法，请使用List <？>：
```java
public static void printList(List<?> list) {
    for (Object elem: list)
        System.out.print(elem + " ");
    System.out.println();
}

```
因为对于任何具体类型A，List &lt;A>是List <？>的子类型，您可以使用printList打印任何类型的list：

```java
List<Integer> li = Arrays.asList(1, 2, 3);
List<String>  ls = Arrays.asList("one", "two", "three");
printList(li);
printList(ls);

```
>本课程中的示例中使用了Arrays.asList方法。这个静态工厂方法转换指定的数组并返回一个固定大小的list。

请注意，List &lt;Object>和List <？>不一样。您可以将Object或Object的任何子类型插入到List &lt;Object>中。但是您只能将null插入List<？>。

# 下边界通配符
下边界通配符将未知类型限制为该类型的指定类型或父类型。下限通配符用通配符（'？'）表示，后跟super关键字，后跟其下界：<？super A>。
>您可以指定通配符的上限，也可以指定下限，但不能同时指定

假设你想编写一个将Integer对象放入list的方法。为了最大化代码的灵活性，您希望该方法适用于List &lt;Integer>，List &lt;Number>和List &lt;Object> - 任何可以保存Integer值的内容。
要想写一个适用于包含Integer的list以及Integer的父类型，比如Integer，Number和Object，您可以指定List <？super Integer>。List&lt;Integer>比List<? Super Integer>限制更大，因为前者只匹配一个Integer类型的list，而后者匹配任何Integer以及其子类型的list。
以下代码将数字1到10添加到列表的末尾：

```java
public static void addNumbers(List<? super Integer> list) {
    for (int i = 1; i <= 10; i++) {
        list.add(i);
    }
}
```
# 通配符和子类型
如泛型，继承和子类型所述，泛型类或接口不会仅仅因为它们之间类型存在关系而相关。但是，您可以使用通配符来创建泛型类或接口之间的关系。
给定以下两个常规（非泛型）类：  
class A { /* ... */ }  
class B extends A { /* ... */ }  

编写以下代码是合理的：  
B b = new B();  
A a = b; 

此示例显示，常规类的继承遵循这个子类型规则：如果B继承A，则类B是类A的子类型。但是此规则不适用于泛型类型：
```java
List<B> lb = new ArrayList<>();
List<A> la = lb;   // compile-time error
```
鉴于Integer是Number的子类型，List &lt;Integer>和List &lt;Number>之间的关系是什么？
![generics-listParent](/images/java base/generics-listParent.gif)
虽然Integer是Number的子类型，但List &lt;Integer>不是List &lt;Number>的子类型，实际上这两种类型不相关。 List &lt;Number>和List &lt;Integer>的公共父项是List <？>。  
为了创建这些类之间的关系，代码可以通过List &lt;Integer>的元素访问Number的方法，使用上限通配符：

```java
List<? extends Integer> intList = new ArrayList<>();
List<? extends Number>  numList = intList;  // OK. List<? extends Integer> is a subtype of List<? extends Number>
```
因为Integer是Number的子类型，而numList是Number对象的List，所以现在在intList（Integer对象List）和numList之间存在关系。下图显示了使用上边界和下边界通配符声明的几个List类之间的关系。
![generics-wildcardSubtyping](/images/java base/generics-wildcardSubtyping.gif)

# 通配符捕获和帮助方法
在某些情况下，编译器推断通配符的类型。例如，一个list可以被定义为List <？>，但是当评估表达式时，编译器从代码中推断出特定类型。这种情况称为通配符捕获。

在大多数情况下，您不需要担心通配符捕获，除非您看到包含短语“捕获（capture of）”的错误消息。

WildcardError示例在编译时会产生捕获错误：

```java
import java.util.List;

public class WildcardError {

    void foo(List<?> i) {
        i.set(0, i.get(0));
    }
}

```
在本示例中，编译器将输入参数i处理为Object类型。当foo方法调用List.set（int，E）时，编译器无法确认正在插入到list中的对象的类型，并产生错误。当发生这种类型的错误时，通常意味着编译器认为您将错误的类型分配给变量。之所以在Java语言中添加泛型是为了在编译时强制保证类型安全。 

WildcardError示例在使用Oracle JDK 7 javac实现编译时产生以下错误：

```bash
WildcardError.java:6: error: method set in interface List<E> cannot be applied to given types;
    i.set(0, i.get(0));
     ^
  required: int,CAP#1
  found: int,Object
  reason: actual argument Object cannot be converted to CAP#1 by method invocation conversion
  where E is a type-variable:
    E extends Object declared in interface List
  where CAP#1 is a fresh type-variable:
    CAP#1 extends Object from capture of ?
1 error

```
在这个例子中，代码正在尝试执行安全操作，那么你如何解决编译器错误？您可以通过编写私有的捕获通配符帮助方法来修复它。在这种情况下，您可以通过创建私有帮助方法fooHelper来解决问题，如WildcardFixed所示：

```java
public class WildcardFixed {

    void foo(List<?> i) {
        fooHelper(i);
    }
  // Helper method created so that the wildcard can be captured
    // through type inference.
    private <T> void fooHelper(List<T> l) {
        l.set(0, l.get(0));
    }

}

```
由于帮助方法，编译器使用推断来确定T是调用中的CAP＃1（捕获变量）。该示例现在已成功编译。
按照惯例，帮助方法通常被命名为originalMethodNameHelper。
现在考虑一个更复杂的例子，WildcardErrorBad：

```java
import java.util.List;

public class WildcardErrorBad {

    void swapFirst(List<? extends Number> l1, List<? extends Number> l2) {
      Number temp = l1.get(0);
      l1.set(0, l2.get(0)); // expected a CAP#1 extends Number,
                            // got a CAP#2 extends Number;
                            // same bound, but different types
      l2.set(0, temp);	    // expected a CAP#1 extends Number,
                            // got a Number
    }
}

```
在这个例子中，代码正在尝试不安全的操作。例如，考虑以下调用swapFirst方法：

```java
List<Integer> li = Arrays.asList(1, 2, 3);
List<Double>  ld = Arrays.asList(10.10, 20.20, 30.30);
swapFirst(li, ld);

```
虽然List &lt;Integer>和List &lt;Double>都符合List<？extends Number>，从Integer的list中获取元素，并尝试将其放入Double值list中显然是不正确的。    
使用Oracle的JDK javac编译器编译代码会产生以下错误：

```bash
WildcardErrorBad.java:7: error: method set in interface List<E> cannot be applied to given types;
      l1.set(0, l2.get(0)); // expected a CAP#1 extends Number,
        ^
  required: int,CAP#1
  found: int,Number
  reason: actual argument Number cannot be converted to CAP#1 by method invocation conversion
  where E is a type-variable:
    E extends Object declared in interface List
  where CAP#1 is a fresh type-variable:
    CAP#1 extends Number from capture of ? extends Number
WildcardErrorBad.java:10: error: method set in interface List<E> cannot be applied to given types;
      l2.set(0, temp);      // expected a CAP#1 extends Number,
        ^
  required: int,CAP#1
  found: int,Number
  reason: actual argument Number cannot be converted to CAP#1 by method invocation conversion
  where E is a type-variable:
    E extends Object declared in interface List
  where CAP#1 is a fresh type-variable:
    CAP#1 extends Number from capture of ? extends Number
WildcardErrorBad.java:15: error: method set in interface List<E> cannot be applied to given types;
        i.set(0, i.get(0));
         ^
  required: int,CAP#1
  found: int,Object
  reason: actual argument Object cannot be converted to CAP#1 by method invocation conversion
  where E is a type-variable:
    E extends Object declared in interface List
  where CAP#1 is a fresh type-variable:
    CAP#1 extends Object from capture of ?
3 errors

```
对于这种问题没有帮助方法可以使用，因为代码设计从根本上就错了。

# 通配符使用指南
学习使用泛型进行编程时，更令人困惑的方面之一是确定何时使用上限通配符以及何时使用下限通配符。本页提供了设计代码时遵循的一些指导原则。
为了讨论的目的，将变量视为提供两个功能之一是有帮助的：

- **一个”in”变量**   
”in”变量为代码提供数据。想象一下有两个参数的复制方法：copy（src，dest）。 src参数提供要复制的数据，因此它是“in”参数。
- **一个”out”变量**    
“out”变量保存在其他地方使用的数据。在复制示例中，copy（src，dest），dest参数接受数据，所以它是“out”参数。

当然，一些变量同时用于“in”和“out”的目的 - 这种情况也在指南中解决。
在决定是否使用通配符以及通配符是什么类型时，可以使用“in”和“out”原则。以下列表提供了以下准则：
- “in”变量用上限通配符定义，使用“extends”关键字
- “out”变量用下限通配符定义，使用“super”关键字
- 在“in”变量可以访问使用Object类中定义的方法情况下，使用无界通配符。
- 在代码需要访问变量作为“in”和“out”变量的情况下，请勿使用通配符。

这些准则不适用于方法的返回类型。应该避免使用通配符作为返回类型，因为它会强制程序员使用代码来处理通配符。
一个被定义为List<? Extends …>的list可以非正式地认为是只读，但这不是一个严格的保证。假设你有以下两个类：
```java
class NaturalNumber {

    private int i;

    public NaturalNumber(int i) { this.i = i; }
    // ...
}

class EvenNumber extends NaturalNumber {

    public EvenNumber(int i) { super(i); }
    // ...
}

```
请考虑以下代码：
```java
List<EvenNumber> le = new ArrayList<>();
List<? extends NaturalNumber> ln = le;
ln.add(new NaturalNumber(35));  // compile-time error

```
因为List &lt;EvenNumber>是List<? extends NaturalNumber>的子类型，可以将le分配给ln。但是您不能使用ln将natural number添加到even numbers的list中。但list中的以下操作是可能的：
- 你可以添加 null.
- 你可以调用clear方法.
- 你可以获取iterator并且调用它的remove方法.
- 您可以捕获通配符并写入从list中读取的元素。

你可以看到定义为List<? extends NaturalNumber>的list不是最严格意义上的只读，您可能会想到这样，因为您不能存储新元素或更改list中的现有元素。

