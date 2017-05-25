title: java 日志（二）
id: 1495623286554
author: 不识
date: 2017-05-24 19:11:26
tags:
---
下图是Java Logging API的工作图 
![Logging Overview](/images/java base/Logging Overview.png)
所有的日志记录都是通过Logger实例来完成。Logger收集要记录的日志数据到一个LogRcord对象中。然后LogRecord被转发到一个Handler。Handler决定如何来处理LogRecord。比如将LogRecord写入到硬盘，或者通过网络发送到监控系统。

Logger和Handler都可以通过一个Filter来传递LogRecord。而Filter是来决定LogRecord是否应该被转发。

一个Handler还可以在LogRecord被发送到外部硬盘或系统前，使用Formatter将其格式化为一个字符串。
<!-- more -->

# 基本使用

Java Logging API的最常使用方式是在每个我们需要记录日志的类里创建一个Logger对象。该Logger实例通常我们会用static和final修饰，这意味着所有这个类的实例都使用同一个Logger实例。
Logger类并没有公开构造器，我们要想创建一个Logger对象，需要使用它的两个静态方法

- getLogger(String name)
- getLogger(String name, String resourceBundleName)

前面我们知道Logger使用分级式以点分割的命名空间，通常基于所在类的包名，下面是一个示例

```java
public class LoggingExamples {

    private static final Logger logger =
        Logger.getLogger(LoggingExamples.class.getName());

}
```

## 日志记录方法
Logger记录日志信息的方法有很多，主要的有下面这些

 
 

   

entering(String sourceClass, String sourceMethod);  
entering(String sourceClass, String sourceMethod, Object param1);  
entering(String sourceClass, String sourceMethod, Object[] params);  

exiting (String sourceClass, String sourceMethod);  
exiting (String sourceClass, String sourceMethod, Object result);  

fine   (String message);  
fine   (String message);  
finest  (String message);  

config  (String message);  
info   (String message);  
warning (String message);  
severe  (String message);  

throwing(String sourceClass, String sourceMethod, Throwable t);


## log()方法
```java
log  (Level level, String message);  
log  (Level level, String message, Object param1);  
log  (Level level, String message, Object[] params);  

log  (Level level, String message, Throwable t);  

log  (LogRecord record);
```

log()方法会在一定日志级别上记录日志信息。log()方法会接收一个Level类型参数作为日志级别，一般都是使用Level中的常量，Level预定义了其中日志级别常数。

一些log()方法会接收一个Object类型参数，在日志被记录前，这个Object类型参数会被插入到日志消息指定的位置中。但是这种合并需要该日志消息没有被Filter过滤掉，或者没有因为日志等级太低没有被记录。这种机制可以提高系统性能。

下面是log(Level level,String message)方法的示例代码
```java
Logger logger = Logger.getLogger(LoggingCase.class.getName());
logger.log(Level.SEVERE,"logMessage");
```
控制台输出（没有设置Handler时会使用父级默认的ConsoleHandler）
>五月 25, 2017 7:19:36 下午 >com.java.logging.LoggingCase doIt  
>严重: logMessage  

然后是log(Level level, String message, Object param1);
```java
logger.log(Level.SEVERE,"logMessage {0}","Object param");
```
控制台输出

>五月 25, 2017 7:28:33 下午 com.java.logging.LoggingCase main  
>严重: logMessage :Object param   

这里会将Object参数插入到消息字符串中，消息字符串要使用{0}形式指定其位置，如果要使用Object[]参数，则使用{0}，{1}...分别指定每一个元素

还有一种log(Level level, String message, Throwable t)方法形式，这种适合做异常日志记录，放在try-catch代码块总，捕获异常时，输出到日志;
比如
```java
        try {
            int a = 1/0;
        } catch (Exception e){
            logger.log(Level.SEVERE,"math error",e);
        }
```
控制台输出
>五月 25, 2017 7:37:57 下午 com.java.logging.LoggingCase main  
严重: math error  
java.lang.ArithmeticException: / by zero  
	at com.java.logging.LoggingCase.main(LoggingCase.java:22)  
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)    
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)  
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)  
	at java.lang.reflect.Method.invoke(Method.java:497)    
	at com.intellij.rt.execution.application.AppMain.main(AppMain.java:147)   

## logp()方法
```java
logp (Level level, String sourceClass, String sourceMethod, String msg);  
logp (Level level, String sourceClass, String sourceMethod, String msg,Object param1);
logp (Level level, String sourceClass, String sourceMethod, String msg,Object[] params);  
logp (Level level, String sourceClass, String sourceMethod, String msg,Throwable t); 
```
logp()方法同log()方法类似，出来每个方法都会多接收两个参数sourceClass和sourceMethod参数。

这两个参数用来标识日志记录调用所在的类和方法名。

## logrb()方法
```java
logrb(Level level, String sourceClass, String sourceMethod,String bundle, String msg);   
logrb(Level level, String sourceClass, String sourceMethod,String bundle, String msg, Object param1);   
logrb(Level level, String sourceClass, String sourceMethod,String bundle, String msg, Object[] params);   
logrb(Level level, String sourceClass, String sourceMethod,String bundle, String msg, Throwable t); 

logrb(Level level, String sourceClass, String sourceMethod, ResourceBundle bundle, String msg, Object... params)
logrb(Level level, String sourceClass, String sourceMethod, ResourceBundle bundle, String msg, Throwable thrown)
logrb(Level level, ResourceBundle bundle, String msg, Object... params)
logrb(Level level, ResourceBundle bundle, String msg, Throwable thrown)
```

logrb()方法和logp()方法也类似，但是一种多了一个String bundle参数，一种多了一个ResourceBundle bundle参数。Sting bundle参数表示一个资源束（resource bundle）所在的位置，资源束是一组包含很多键值对的文本文件，就像property属性文件一样，每个这样的文本文件都包含相同的键，但是它对应的值，在不同的文件使用了不同的语言资源束是用来做国际化功能的，但这种方法已经被弃用，在Java 8后新添了ResourceBundle类，由一个ResourceBundle来代表一个资源束文件

假如我们有一个资源束文件resources.myresources,其内容是
>key1 : This is message 1  
key2 : this is message 2  

然后使用日志
```java
logger.logrb(Level.SEVERE, "logging.LoggingExamples", "main",
        "resources.myresources", "key1");

```
控制台会输出




# 翻译来源
>[Java Logging--Jakob Jenkov](http://tutorials.jenkov.com/java-logging/index.html)