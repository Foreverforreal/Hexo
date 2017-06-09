title: java 泛型（二）原生类型
id: 1496825267999
author: 不识
date: 2017-06-07 16:47:50
tags:
  - java
  - 泛型
categories:
  - java 基础
  - java 泛型
---

# 原生类型
原生类型（raw type）是没有任何类型参数的泛型类或接口的名称。例如，给定泛型Box类：
```java
public class Box<T> {
    public void set(T t) { /* ... */ }
    // ...
}
```
<!-- more -->
要创建一个参数化类型的Box &lt;T>，您可以为形式类型参数T提供一个实际的类型参数：
```java
Box<Integer> intBox = new Box<>();
```
如果实际的类型参数被省略，您将创建一个Box &lt;T>的原生类型：
```java
Box rawBox = new Box();
```
因此，Box是泛型类型Box &lt;T>的原生类型。但是，非泛型类或接口类型不是原生类型。
原生类型存在于许多遗留代码中，因为许多API类（如Collections类）在JDK 5.0之前不是泛型的。当使用原生类型时，您实质上是获得了一个预泛型 - 一个提供Object的Box。为了向后兼容，允许将参数化类型分配给其原生类型：
```java
Box<String> stringBox = new Box<>();
Box rawBox = stringBox;               // OK
```
但是，如果将原始类型分配给参数化类型，则会收到警告：
```java
Box rawBox = new Box();           // rawBox is a raw type of Box<T>
Box<Integer> intBox = rawBox;     // warning: unchecked conversion

```

如果您使用原生类型来调用在相应的泛型类型中定义的泛型方法，则还会收到警告：

```java
Box<String> stringBox = new Box<>();
Box rawBox = stringBox;
rawBox.set(8);  // warning: unchecked invocation to set(T)
```

警告显示，原生类型绕过泛型类型检查，将不安全代码的捕获推迟到运行时。因此，您应避免使用原生类型。
在类型擦除章节有关于Java编译器如何使用原生类型的更多信息。

# 未检查错误信息

如前所述，当将旧代码与泛型代码混用时，您可能会遇到类似于以下内容的警告消息：  
**Note: Example.java uses unchecked or unsafe operations.   
Note: Recompile with -Xlint:unchecked for details.   **
当使用旧的API对一个原生类型进行操作的时候，可能会发生这种情况，如以下示例所示：  
```java
public class WarningDemo {
    public static void main(String[] args){
        Box<Integer> bi;
        bi = createBox();
    }

    static Box createBox(){
        return new Box();
    }
}
```
“unchecked”表示编译器没有足够的类型信息来执行所有必要的类型检查以确保类型安全。尽管编译器提供了一个提示，但是默认情况下，“unchecked”警告是被禁用的。要查看所有“unchecked”警告，请使用Xlint:unchecked重新编译。   
使用-Xlint：unchecked重新编译前面的示例可以显示以下附加信息：
``` bash
WarningDemo.java:4: warning: [unchecked] unchecked conversion
found   : Box
required: Box<java.lang.Integer>
        bi = createBox();
                      ^
1 warning
```
要完全禁用“unchecked”警告，请使用-Xlint：unchecked标志。@SuppressWarnings（“unchecked”）注释也会抑制“unchecked”的警告。如果您不熟悉@SuppressWarnings语法，请参阅注释。


