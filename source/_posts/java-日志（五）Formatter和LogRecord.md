title: java 日志（五）Formatter和LogRecord
id: 1495796101086
author: 不识
date: 2017-05-26 19:29:54
tags:
  - java
  - logging
categories:
  - java 基础
  - java 日志
---
# LogRecord

LogRecord对象用于在日志框架和各个日志handler之间传递日志请求,它包装了一系列的日志记录信息。LogRecord有许多get方法来获取这些信息，比如

```java
getLevel()
getLoggerName()
getMessage()
getMillis()
getParameters()
getResourceBundle()
getResourceBundleName()
getSequenceNumber()
getSourceClassName()
getSourceMethodName()
getThreadID()
getThrown()
```
<!-- more -->
每个get方法都有一个相应的set方法，但是除非我们自己创建一个LogRecord对象，否则不需要使用这些set方法，并且当LogRecord被传递到日志框架中时，它逻辑上属于框架，也不应再被客户端应用程序使用或更新。  
下面是这些方法的简介
- **getLevel()**  获取这条消息记录的日志等级。  
- **getLoggerName()** 返回记录这条消息的Logger的名字。
- **getMesseage()**  获取在本地化或格式化之前获取“原始”日志消息，是一个字符串形式。  
- **getMillis()**  方法返回记录LogRecord的时间（以毫秒为单位）。
- **getParameters()**  返回要插入此LogRecord消息的参数。
- **getResourceBundle()**  方法返回用于本地化LogRecord消息的ResourceBundle（如果有的话）。如果没有使用ResourceBundle，则此方法返回null。 
- **getResourceBundleName()**  返回用于本地化LogRecord消息的ResourceBundle（如果有）的名称。如果没有使用ResourceBundle，则返回null。
- **getSequenceNumber()**  方法返回当创建LogRecord时，在LogRecord构造函数内部生成的序列号。
- **getSourceClassName()**和**getSourceMethodName()**  分别返回记录该消息所在的类名和方法名，它或者是由我们调用log方法时手动传入的，或者是由虚拟机自动添加上去。需要注意的是当由虚拟机自动添加时，该信息不能保证准确。
- **getThreadID()** 返回记录由该LogRecord表示的消息的线程的ID。
- **getThrown()** 返回由此LogRecord记录消息时标记的Throwable。这个Throwable要么是直接作为参数传递给Logger日志记录方法，要么是直接设置在LogRecord上，然后传递给Logger。这种Throwable通常是导致要调用日志记录的原因。

# Formatter
Formatter是在Handler中在写入到外部之前用来格式化LogRecord中的信息的。
java.util.logging提供了Formatter抽象类，还为我们提供了两个实现类
- **SimpleFormatter**
- **XMLFormatter**

Java Logging API中各种Handler默认使用上面两种Formatter中的一种用来格式化日志消息。我们还可以实现自己的子类，Formatter中只有一个format(LogRecord record)抽象方法需要我们来实现，如下
```java
public class MyFormatter extends Formatter {

    @Override
    public String format(LogRecord record) {
        return record.getLevel() + ":" + record.getMessage();
    }
}
```
子类必须覆盖Formatter类中的抽象方法format()。由format()返回的String是由处理程序转发到外部系统的。字符串应该如何格式化取决于你。
此外Formatter中还给子类提供了一个便易方法formatMessage(LogRecord record)，它从LogRecord中本地化和格式化消息字符串，子类实现时我们可以直接调用
```java
public class MyFormatter extends Formatter {

    @Override
    public String format(LogRecord record) {
		return formatMessage(record);
}
```