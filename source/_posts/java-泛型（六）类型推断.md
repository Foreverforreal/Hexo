title: java 泛型（六）类型推断
id: 1496976973723
author: 不识
date: 2017-06-09 10:56:15
tags:
  - java
  - 泛型
categories:
  - java 基础
  - java 泛型
---
类型推断是Java编译器查看每个方法调用和相应声明的能力，以确定调用适用的类型参数（或参数）。推断算法确定参数的类型，如果可用的话，这个类型将被分配或返回。最后，推断算法尝试找到适用于所有参数的最适应类型。
为了说明这个最后一点，在下面的例子中，推断确定传递给pick方法的第二个参数的类型是Serializable：
```java
static <T> T pick(T a1, T a2) { return a2; }
Serializable s = pick("d", new ArrayList<String>());
```
在这种情况下，我们使用两种不同类型的T：String和ArryaList。编译器然后使用最普适的类型参数，使得方法调用类型正确。在这种情况下，它推断T是两种类型共同实现接口类型Serializable。

# 类型推断和泛型方法

在泛型方法中引入了类型推断，这使您能够像普通的方法一样调用泛型方法，而不在尖括号之间指定类型，考虑下面的例子BoxDemo，它需要Box类：
```java
public class BoxDemo {

  public static <U> void addBox(U u, 
      java.util.List<Box<U>> boxes) {
    Box<U> box = new Box<>();
    box.set(u);
    boxes.add(box);
  }

  public static <U> void outputBoxes(java.util.List<Box<U>> boxes) {
    int counter = 0;
    for (Box<U> box: boxes) {
      U boxContents = box.get();
      System.out.println("Box #" + counter + " contains [" +
             boxContents.toString() + "]");
      counter++;
    }
  }

  public static void main(String[] args) {
    java.util.ArrayList<Box<Integer>> listOfIntegerBoxes =
      new java.util.ArrayList<>();
    BoxDemo.<Integer>addBox(Integer.valueOf(10), listOfIntegerBoxes);
    BoxDemo.addBox(Integer.valueOf(20), listOfIntegerBoxes);
    BoxDemo.addBox(Integer.valueOf(30), listOfIntegerBoxes);
    BoxDemo.outputBoxes(listOfIntegerBoxes);
  }
}
```
以下是此示例的输出：  
Box #0 contains [10]   
Box #1 contains [20]  
Box #2 contains [30]  
泛型方法addBox定义了一个名为U的类型参数。通常，Java编译器可以推断泛型方法调用的类型参数。因此，在大多数情况下，您不必指定它们。例如，要调用泛型方法addBox，可以使用类型见证指定类型参数，如下所示：  
```java
BoxDemo.<Integer>addBox(Integer.valueOf(10), listOfIntegerBoxes);
```
或者，如果省略类型见证，Java编译器会自动推断（从方法的参数），类型参数是Integer：
```java
BoxDemo.addBox(Integer.valueOf(20), listOfIntegerBoxes);
```

# 类型推断和泛型类

只要编译器可以从上下文中推断出类型参数，就可以使用空的一组类型参数（<>）替换调用泛型构造函数所需的类型参数。这对尖括号是非正式地称为菱形。

例如，考虑以下变量声明：
```java
Map<String, List<String>> myMap = new HashMap<String, List<String>>();
```
您可以使用空的一组类型参数（<>）替换构造函数的参数化类型：
```java
Map<String, List<String>> myMap = new HashMap<>();
```

请注意，要在泛型类实例化过程中使用类型推断，您必须使用菱形符号。在以下示例中，编译器生成未经检查的转换警告，因为HashMap（）构造函数引用了HashMap原生类型，而不是Map &lt;String，List &lt;String >>类型： 
```java
Map<String, List<String>> myMap = new HashMap(); // unchecked conversion warning
```

# 类型推断和泛型类以及非泛型类的泛型构造方法

请注意，在泛型类以及非泛型类中，构造方法都可以是泛型（换而言之，声明自己的形式类型参数）。考虑以下示例
```java
class MyClass<X> {
  <T> MyClass(T t) {
    // ...
  }
}
```
考虑下面的类MyClass的实例化：
new MyClass&lt;Integer>("")
此语句创建一个参数化类型MyClass &lt;Integer>的实例; 该语句显式指定了泛型类MyClass &lt;X>的形式类型参数X的类型为Integer。请注意，此泛型类的构造函数包含一个形式类型参数T. 编译器推断出此泛型类的构造函数的形式类型参数T的类型为String（因为此构造函数的实际参数是String对象）。
在Java SE 7之前的版本的编译器能够推断泛型构造函数的实际类型参数，类似于泛型方法。但是，J在ava SE 7及更高版本中如果您使用菱形符号（&lt;>），编译器可以推断要实例化的泛型类的实际类型参数。请考虑以下示例：
```java
MyClass<Integer> myObject = new MyClass<>("");
```
在这个例子中，编译器推断泛型类Myclass&lt;x>中形式参数X的类型为Integer。它也推断出泛型类的构造方法的形式参数T额类型为String。
>需要注意的是，推断算法只使用调用参数，目标类型以及可能的明显的预期返回类型来推断类型。推断算法不使用程序后期的结果。

# 目标类型

Java编译器利用目标类型来推断泛型方法调用的类型参数。表达式的目标类型是Java编译器期望的数据类型，具体取决于表达式的出现位置。考虑方法Collections.emptyList，其声明如下：
```java
static <T> List<T> emptyList();
```
考虑以下赋值语句：
```java
List<String> listOne = Collections.emptyList();
```
此语句期待List &lt;String>的实例;这个数据类型就是是目标类型。因为方法emptyList返回一个类型为List &lt;T>的值，编译器会推断类型参数T一定是值String。这可以在Java SE 7和8中使用。或者，您可以使用类型见证并指定T的值如下：
```java
List<String> listOne = Collections.<String>emptyList();
```
但是，在这种情况下这不是必需的。但在其他情况下是有必要的。请考虑以下方法：
```java
void processStringList(List<String> stringList) {
    // process stringList
}
```
假设你想使用一个空List调用processStringList方法。在Java SE 7中，以下语句不能编译：
```java
processStringList(Collections.emptyList());
```
Java SE 7编译器会生成类似于以下内容的错误消息：
``` java
List<Object> cannot be converted to List<String>
```

编译器需要一个类型参数T的值，所以它从值Object开始。因此，对Collections.emptyList的调用返回一个类型为List &lt;Object>的值，它与processStringList方法不兼容。因此，在Java SE 7中，必须指定type参数的值的值如下：
```java
processStringList(Collections.<String>emptyList());
```