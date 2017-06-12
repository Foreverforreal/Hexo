title: java 泛型（四）有界类型参数
id: 1496827549703
author: 不识
tags:
  - java
  - 泛型
categories:
  - java 基础
  - java 泛型
date: 2017-06-07 17:25:51
---
可能有时候要限制在参数化类型中可以用作类型参数的类型。例如，一个对数字进行操作的方法可能只希望接受Number或其子类的实例。这就是有界类型参数。
要声明一个有界类型的参数，需要列出类型参数的名称，后跟extends关键字，再后跟其上边界，在此示例中为Number。请注意，在这种情况下，extends在一般意义上用于表示“extends”（如在类中）或“implements”（如在接口中）。
<!-- more -->
```java
public class Box<T> {

    private T t;          

    public void set(T t) {
        this.t = t;
    }

    public T get() {
        return t;
    }

    public <U extends Number> void inspect(U u){
        System.out.println("T: " + t.getClass().getName());
        System.out.println("U: " + u.getClass().getName());
    }

    public static void main(String[] args) {
        Box<Integer> integerBox = new Box<Integer>();
        integerBox.set(new Integer(10));
        integerBox.inspect("some text"); // error: this is still String!
    }
}
```

在修改我们的泛型方法，添加了这个有界类型的参数后，编译现在将失败，因为我们在调用inspect时传入了一个String：
``` bash
Box.java:21: <U>inspect(U) in Box<java.lang.Integer> cannot
  be applied to (java.lang.String)
                        integerBox.inspect("10");
                                  ^
1 error

```
除了限制可用于实例化通用类型的类型之外，有界类型参数还允许您调用边界对象中定义的方法：
```java
public class NaturalNumber<T extends Integer> {

    private T n;

    public NaturalNumber(T n)  { this.n = n; }

    public boolean isEven() {
        return n.intValue() % 2 == 0;
    }

    // ...
}
```
isEven方法通过n调用Integer类中定义的intValue方法。

# 多重边界
前面示例中说明了在单边界类型参数的使用，但类型参数可以有多个边界：
> <T extends B1 & B2 & B3>

一个有多个边界的类型变量是在边界中所有类型的子类型。如果其中一个边界是一个类，则必须首先指定。例如：  
Class A { /* ... */ }  
interface B { /* ... */ }  
interface C { /* ... */ }  

class D &lt;T extends A & B & C> { /* ... */ }  
如果指定边界A为第一个，那么就会产生一个编译时期错误:

class D &lt;T extends B & A & C> { /* ... */ }  // compile-time error