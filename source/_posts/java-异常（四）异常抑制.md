title: java 异常（四）异常抑制
id: 1496032917285
author: 不识
tags:
  - java
  - exception
categories:
  - java 基础
  - java 异常
date: 2017-05-29 12:41:59
---
在我们使用一些I/O类的时候，我们必须保证在I/O操作结束后，关闭流来释放资源，看下面代码

```java
public void printFile() throws IOException {
    InputStream input = null;

    try {
        input = new FileInputStream("file.txt");

        int data = input.read();
    } finally {
        if(input != null){
            input.close();
        }
    }
}

```

<!-- more -->
这里在new FileInputStream("file.txt"),input.read()和input.close()处都可能会抛出异常，我们知道无论是否从try块抛出异常，finally块中代码总会执行，这样我们假设在try块中代码出现异常，在抛出异常前执行finally块中代码，如果此时input.close()同样出现了异常，那么最终会抛出哪个异常呢？看下面实验代码

```java
public class ExceptionCase {
    public static void main(String[] args) throws IOException {
        Resource resource = new Resource();
        try {
            resource.doSomething();
        } finally {
            resource.close();
        }
    }
}
class Resource implements AutoCloseable {
    public void doSomething() throws IOException {
        System.out.println("Resource is doing something");
        throw new IOException();
    }

    @Override
    public void close() {
        throw new IndexOutOfBoundsException();
    }
}
```
这里我们自定义了一个类Resource来模拟一个I/O类，它的两个方法都会抛出异常，doSomething()中会抛出IOException，close()方法中将抛出IndexOutOfBoundsException，它是一个非检查型异常，在运行程序后，控制台输出

>Resource is doing something
>Exception in thread "main" java.lang.IndexOutOfBoundsException

可以发现finally块内的异常最终被抛出，而try块中的异常被抑制了，这样会带来一个问题，对于I/O类实际使用中，我们需要处理产生异常的原因在try块中，而抛出的却是finally块中的，这会对我们程序问题分析检查带来误导，为了解决这个问题，应当使用tyr-with-resource。上面代码我们修改为
```java
        try (Resource resource = new Resource()) {
            resource.doSomething();
        }
```
由于Resource类实现了AutoCloseable接口，所以可以声明在try-with-resource语句中，Resource对象的close方法会无论什么情况，最后会被系统自动调用来，此时运行程序，控制台输出
>Resource is doing something  
>Exception in thread "main" java.io.IOException

可以发现try-with-resource语句与try...finally相反，它的close方法抛出异常被抑制，抛出的是代码块内异常，这是try-with-resource的异常处理机制，它只会抛出在代码块内的异常，try()语句中的异常将被抑制，但是被抑制的异常并没有被丢弃，在Java 7后java.lang.Throwable类中新加入两个方法，是我们可以添加或获取抑制异常
- public final void **addSuppressed**(Throwable exception)
- public final Throwable[] **getSuppressed**()

这里需要注意的是，这两个方法需要在异常的构造方法中开启才能使用
- protected Throwable(String message,Throwable cause,boolean enableSuppression,boolean writableStackTrace)

try-with-resource语句中会开启使用这两个方法，并且在try()抛出的异常自动调用addSuppressed()方法将其加入到被抑制异常的数组中,为了获取被抑制的异常，我们将try()中抛出的异常使用catch捕获，而不是在方法体上抛出
```java
        try (Resource resource = new Resource()) {
            resource.doSomething();
        } catch (IOException e) {
            e.printStackTrace();
            Throwable[] suppressed = e.getSuppressed();
            for (Throwable throwable : suppressed) {
                throwable.printStackTrace();
            }
        }
```
此时运行程序，控制台将会输出

>Resource is doing something  
>java.io.IOException  
>　　...  
>java.lang.IndexOutOfBoundsException  
>　　...  

这样就获取到了被抑制的异常信息。