title: java 异常（五）异常层次
id: 1496032935984
author: 不识
date: 2017-05-29 12:42:22
tags:
---


在java中异常可以以分层结构组织，我们可以通过使一个（或多个）异常扩展另一个异常来创建层次结构。这样第一个异常成为第二个异常的子类。在前面也展示过Java中所有的异常都是Exception的子类，异常层次结构的优点是，如果您决定在层次结构中捕获（使用try-catch）某个异常，那么您还将自动捕获该异常的所有子类。比如下面示例代码
```java
		FileInputStream input = null;
        try {
            input = new FileInputStream("c://example.txt");
            input.read();
        } catch (IOException e) {
            e.printStackTrace();
        }
```
上面代码中，有两处会抛出异常的地方
- input = new FileInputStream("c://example.txt");此处会抛出FileNotFoundException
- input.read();此处会抛出IOException
由于IOException是FileNotFoundException的父类，所以代码中使用IOException就可以这两个可能的异常。

## 多catch捕获

在一个try块后可能跟随多个catch代码块，这种形式用在try块中的代码抛出了多种类型的异常。当这些异常存在继承关系时，同样适用多个catch捕获的形式，但是这时需要注意catch的顺序，父类异常必须放在子类异常之后，否则子类异常将无法被对应catcht块捕获，比如上面示例要想用多个catch捕获
```java
 try {
            input = new FileInputStream("c://example.txt");
            input.read();
        } catch (FileNotFoundException e){
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

```
需要注意的是，如果我们想要使用Java 7之后的，一个catch捕获多个异常机制，其异常参数不应当存在父子关系。

## Throws 
同样，当我们在方法声明上抛出一个异常，任何该异常的子类也能够被抛出。
```java
public void doSomething() throws IOException{
}
```
这个示例中，如果方法中有FileNotFoundException异常的话，由于它是IOException的子类，同样可以被正常抛出。当使用throws抛出多个异常，异常之间存在继承关系时，父类应当在子类的后面，否则声明抛出子类是没有意义的，如
```java
public void doSomething() throws IOException, FileNotFoundException{
}
```
这里FileNotFoundException放在IOException的后面，是没有意义的话，因为如果抛出FileNotFoundException的话，它已经会被声明的IOException捕获抛出了。如果调换顺序，则两个抛出声明都有实际作用，FileNotFoundException或被抛出自己以及子类异常，IOException则会抛出自己已经所有除了FileNotFoundException的子类异常。

在设计应用程序API时，应当为该API创建一个基础Exception，这样使用这一个基础Exception就可以处理API中所有类型的异常。当处理异常需要更细的颗粒度时，例如异常应当有不同的处理方式，这是就可以通过拓展基础Exception来添加新的异常类。这样通过层级的形式来构建异常结构，可以方便进行异常的管理与处理。

