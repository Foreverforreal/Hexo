title: java 泛型（九）泛型的限制
id: 1496989759409
author: 不识
date: 2017-06-09 14:29:30
tags:
  - java
  - 泛型
categories:
  - java 基础
  - java 泛型
---


要有效地使用Java泛型，您必须考虑以下限制：
- 无法使用原始类型实例化泛型类型
- 无法创建类型参数实例
- 无法声明类型参数静态字段
- 无法在参数化类型上使用类型转换或instranceof
- 无法创建参数化类型数组
- 无法创建、捕获、或抛出参数化类型对象
- 无法重载一个形式参数被擦除后是相同原生类型的方法

# 无法使用原始类型实例化泛型类型

考虑以下参数化类型
```java
lass Pair<K, V> {

    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    // ...
}
```
在创建一个Pair对象时，你不可以用原始类型来替代类型参数K或者V
```java
Pair<int, char> p = new Pair<>(8, 'a');  // compile-time error
```
	你只可以使用非原始类型来替代类型参数K和V
```java
Pair<Integer, Character> p = new Pair<>(8, 'a');
```
请注意，Java编译器将8自动装箱为Integer.valueOf（8）并且“a”自动装箱为Character（'a'）：
```java
Pair<Integer, Character> p = new Pair<>(Integer.valueOf(8), new Character('a'));
```


# 无法创建类型参数实例

您不能创建一个类型参数的实例。例如，以下代码会导致编译时错误：
```java
public static <E> void append(List<E> list) {
    E elem = new E();  // compile-time error
    list.add(elem);
}
```
作为解决方法，您可以通过反射创建类型参数的对象：
```java
public static <E> void append(List<E> list, Class<E> cls) throws Exception {
    E elem = cls.newInstance();   // OK
    list.add(elem);
}
```
您可以如下调用append方法：
```java
List<String> ls = new ArrayList<>();
append(ls, String.class);
```

# 无法声明类型参数静态字段
一个类的静态字段是由类的所有非静态对象共享的类级变量。因此，不允许使用类型参数的静态字段。考虑下列类：
```java
public class MobileDevice<T> {
    private static T os;

    // ...
}
```
如果允许类型参数的静态字段，那么以下代码将被混淆：
```java
MobileDevice<Smartphone> phone = new MobileDevice<>();
MobileDevice<Pager> pager = new MobileDevice<>();
MobileDevice<TabletPC> pc = new MobileDevice<>();
```
因为静态字段os是由phone，pager和pc共享的，那么os的实际类型是什么？它不能同时是Smartphone，Pager和TabletPC。因此，您不能创建类型参数的静态字段。因此，您不能创建类型参数的静态字段。


# 无法在参数化类型上使用类型转换或instranceof
因为Java编译器会擦除通用代码中的所有类型参数，所以无法验证在运行时使用泛型类型的参数化类型：
```java
public static <E> void rtti(List<E> list) {
    if (list instanceof ArrayList<Integer>) {  // compile-time error
        // ...
    }
}
```
传递给rtti方法的一系列参数化类型是：
```java
S = { ArrayList<Integer>, ArrayList<String> LinkedList<Character>, ... }
```
运行时不跟踪类型参数，因此它不能区分ArrayList &lt;Integer>和ArrayList &lt;String>之间的区别。您可以做的最多的是使用无界通配符来验证list是否为ArrayList：  
```java
public static void rtti(List<?> list) {
    if (list instanceof ArrayList<?>) {  // OK; instanceof requires a reifiable type
        // ...
    }
}
```
 
通常，您不能转换为参数化类型，除非由无界通配符来参数化的。例如：  
```java
List<Integer> li = new ArrayList<>();
List<Number>  ln = (List<Number>) li;  // compile-time error
```
然而，在某些情况下，编译器知道一个类型参数始终是有效的并允许该转换。例如： 
```java
List<String> l1 = ...;
ArrayList<String> l2 = (ArrayList<String>)l1;  // OK

```

# 无法创建参数化类型数组
您不能创建参数化类型的数组。例如，以下代码无法编译：

```java
List<Integer>[] arrayOfLists = new List<Integer>[2];  // compile-time error
```java
以下代码说明了将不同类型插入到数组中时会发生什么：
```java
Object[] strings = new String[2];
strings[0] = "hi";   // OK
strings[1] = 100;    // An ArrayStoreException is thrown.
```
如果您使用泛型列表尝试相同的事情，则会出现问题：
```java
Object[] stringLists = new List<String>[];  // compiler error, but pretend it's allowed
stringLists[0] = new ArrayList<String>();   // OK
stringLists[1] = new ArrayList<Integer>();  // An ArrayStoreException should be thrown,
                                            // but the runtime can't detect it.
```
如果允许使用参数化列表的数组，以前的代码将无法抛出所需的ArrayStoreException。


# 无法创建、捕获、或抛出参数化类型对象
泛型类不能直接或间接继承Throwable类。例如，以下类将不会编译：
```java
// Extends Throwable indirectly
class MathException<T> extends Exception { /* ... */ }    // compile-time error

// Extends Throwable directly
class QueueFullException<T> extends Throwable { /* ... */ // compile-time error
```
一个方法不能捕获一个类型参数的实例：
```java
public static <T extends Exception, J> void execute(List<J> jobs) {
    try {
        for (J job : jobs)
            // ...
    } catch (T e) {   // compile-time error
        // ...
    }
}
```
但是，您可以在throws子句中使用类型参数：
```java
class Parser<T extends Exception> {
    public void parse(File file) throws T {     // OK
        // ...
    }
}
```


# 无法重载一个形式参数被擦除后是相同原生类型的方法

一个类不能有两个在类型擦除后将具有相同的签名重载方法。
```java
public class Example {
    public void print(Set<String> strSet) { }
    public void print(Set<Integer> intSet) { }
}
```
重载将共享相同的类文件表示，并将生成编译时错误。


