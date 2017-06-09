title: java 泛型（三）泛型方法
id: 1496827375364
author: 不识
date: 2017-06-07 17:22:58
tags:
  - java
  - 泛型
categories:
  - java 基础
  - java 泛型
---



泛型方法是引入自己的类型参数的方法。这类似于声明泛型类型，但类型参数的范围仅限于声明它的方法。允许静态和非静态泛型方法，以及泛型类构造函数。
泛型方法的语法包括一个类型参数，位于尖括号内，并出现在方法的返回类型之前。对于静态泛型方法，类型参数部分必须出现在方法的返回类型之前。   
Util类包括一个泛型的方法compare，用来比较两个Pair对象：   
```java
public class Util {
    public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
               p1.getValue().equals(p2.getValue());
    }
}

public class Pair<K, V> {

    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public void setKey(K key) { this.key = key; }
    public void setValue(V value) { this.value = value; }
    public K getKey()   { return key; }
    public V getValue() { return value; }
}
```

调用此方法的完整语法是：
```java
Pair<Integer, String> p1 = new Pair<>(1, "apple");
Pair<Integer, String> p2 = new Pair<>(2, "pear");
boolean same = Util.<Integer, String>compare(p1, p2);
```
上面泛型方法显示给出了类型参数，但一般来说，这可以省略，编译器会推断需要的类型：
```java
Pair<Integer, String> p1 = new Pair<>(1, "apple");
Pair<Integer, String> p2 = new Pair<>(2, "pear");
boolean same = Util.compare(p1, p2);
```
此功能称为类型推断（type inference），允许您以常规方法调用泛型方法，而不在尖括号内指定类型。   
