title: java 8 Lambda表达式(翻译自Stackoverflow)
id: 1493432350174
author: 不识
tags:
  - java
  - Lambda
categories:
  - java其他
date: 2017-04-29 10:19:00
---
# 函数式接口
***

[原文链接](http://stackoverflow.com/documentation/java/91/lambda-expressions#t=201703170153070549388)Lambda只能作用于一个只有一个抽象方法的函数式接口(Function Interface),不过函数式接口可以有任意数量default或static修饰的方法(因此,它们有时也被当做单抽象方法类型接口或者说是SAM类型接口)

``` java
interface Foo1 {
    void bar();
}

interface Foo2 {
    int bar(boolean baz);
}

interface Foo3 {
    String bar(Object baz, int mink);
}

interface Foo4 {
    default String bar() { // default so not counted
        return "baz";
    }
    void quux();
}
```
当我们声明一个函数式接口,可以添加上@FunctionalInterface声明符.这并没有什么特别的效果,只是添加了这个声明符的接口如果不是函数式的,那么编译器便会报错,因此它实际是一个提醒,告诉我们这个接口不应该被修改(被添加更多的抽象方法).

<!-- more -->
```java 
@FunctionalInterface
interface Foo5 {
    void bar();
}

@FunctionalInterface
interface BlankFoo1 extends Foo3 { // 从Foo3中继承抽象方法
}

@FunctionalInterface
interface Foo6 {
    void bar();
    boolean equals(Object obj); // 重写Object的方法并不会被计入
}
```
相反的,下面并不是一个函数式接口,因为它有超过一个的抽象方法

```java 
interface BadFoo {
    void bar();
    void quux(); // <-- 第二个方法会导致禁用lambda,因为lambda会不知道到底要实现哪一个抽象方法
}
```
下面同样不是一个函数式接口,因为它没有任何方法

```java 
interface BlankFoo2 { }
```
注意下面的例子,假设你有一个接口

```java 
interface Parent { public int parentMethod(); }
```
并且有一个子接口继承自它

```java 
interface Child extends Parent { public int ChildMethod(); }
```
那么这个子类并不是一个函数式接口因为它有两个方法

Java 8同样在java.util.funciton中提供了一系列的通用函数式接口模版.例如内置接口Predicate&lt;T&gt;,它只包含一个方法,这个方法需要传入一个T类型参数,并返回一个布尔类型的值.

# Lambda表达式
***
下面是Lambda表达式的基本结构

![Lambda表达式](https://i.stack.imgur.com/RRcfc.png?_=6564813)
上图中fi是一个类的单例,就和实现了**FuctionalInterface**接口的匿名类一样,它以{System.out.println("Hello"); }这段方法体重写了接口的抽象方法,换而言之,上面的等同于
```java 
FunctionalInterface fi = new FunctionalInterface() {
    @Override
    public void theOneMethod() {
        System.out.println("Hello");
    }
};
```
Lambda表达式只是等效于一个匿名类,因为在Lambda表达式中,像this,super或toString()这些引用的是被调用的类本身,而不是新建的对象.

使用Lambda表达式你可以不必特别指定方法的名称---这也不需要,因为在一个函数式接口中有且只有一个抽象方法,这样java也只会重写这一个;

在这种情况下,Lambda表达式的类型并不确定(如重载方法),因此你可以在Lambda表达式中添加一个类型转换,来告诉编译器它应该是什么类型,就如下面例子一样
```java 
Object fooHolder = (Foo1) () -> System.out.println("Hello");
System.out.println(fooHolder instanceof Foo1); // returns true
```
如果函数式接口的方法有参数的话,那么参数名应该写在Lambda表达式括号中.这里并不需要声明参数或返回值的类型,因为java会自动从接口中获取(如果愿意声明的话,即使你声明了类型,java并也不会报错),因此,下面两个例子是等效的
```java 
Foo2 longFoo = new Foo2() {
    @Override
    public int bar(boolean baz) {
        return baz ? 1 : 0;
    }
};
Foo2 shortFoo = (x) -> { return x ? 1 : 0; };
```
如果这个方法只有一个参数的话,参数外的括号也可以省略

```java 
Foo2 np = x -> { return x ? 1 : 0; }; // 可以省略
Foo3 np2 = x, y -> x.toString() + y    //error 不可以省略
```

# 隐式返回
***
如果lambda表达式内的代码是一个java表达式而不是代码块的话,它会被当做一个方法返回的表达式值.因此下面两个例子是等效的
```java 
IntUnaryOperator addOneShort = (x) -> (x + 1);
IntUnaryOperator addOneLong = (x) -> { return (x + 1); };
```
# 访问局部变量(闭包值)
***

lambda表达式只是匿名类简写的语法糖,因此它也同样遵守访问作用域内局部变量的规则;局部变量必须被当做以final关键字修饰,在lambda表达式中无法被修改

```java 
IntUnaryOperator makeAdder(int amount) {
    return (x) -> (x + amount); // 这是合法的,即使amount会超出作用域,因为amount没有被修改
}

IntUnaryOperator makeAccumulator(int value) {
    return (x) -> { value += x; return value; }; // error  将无法编译通过
}
```
如果有必要以这种方式包含一个变量,应该用一个普通对象复制该变量,想了解更多请查看Java Closures with lambda expressions.;

# 接收Lambda表达式
***

因为lambda是一个接口的实现,所以没有特别需要做的当一个方法接收lambda作为参数时:任何一个接收函数式接口的方法都可以接收lambda表达式

```java 
public void passMeALambda(Foo1 f) {
    f.bar();
}
passMeALambda(() -> System.out.println("Lambda called"));
```
# Lambda表达式的类型
***

一个lambda表达式自己没有什么特定的类型,显然的是,参数的数量和类型,连同返回值的类型能够传达一些信息,这些信息能够唯一限制它将被赋予的类型.当lambda用以下一些方式被分配给一个函数式接口类型时,它将接收一个特定类型;

- 直接被赋值一个函数类型,比如  myPredicate = s -> s.isEmpty()
- 将它作为参数传递当需要一个函数类型参数时,比如 stream.filter(s -> s.isEmpty())
- 将它作为返回值从一个返回函数类型的方法中返回,比如 return s -> s.isEmpty()
- 将它转换为一个函数类型,比如 (Predicate&lt;String&gt;) s -> s.isEmpty()
- 直到任何一个赋值给函数类型操作执行前,lambda都没有一个确定类型.为了举例,可以看下这个lambda表达式 o -> o.isEmpty().相同的lambda表达式会被赋予不同的函数类型.

```java 
Predicate<String> javaStringPred = o -> o.isEmpty();
Function<String, Boolean> javaFunc = o -> o.isEmpty();
Predicate<List> javaListPred = o -> o.isEmpty();
Consumer<String> javaStringConsumer = o -> o.isEmpty(); // 返回值被忽略
com.google.common.base.Predicate<String> guavaPredicate = o -> o.isEmpty();
```
现在他们被指定类型,这些例子展示了即使看起来一样的lambda表达式,但他们是完全不同的类型,不能够相互之间再被赋值;