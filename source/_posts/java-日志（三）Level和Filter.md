title: java 日志（三）Level和Filter
id: 1495778999893
author: 不识
date: 2017-05-26 14:10:02
tags:
  - java
  - logging
categories:
  - java 基础
  - java 日志
---

Level和Filter都可以控制一个LogRecord是否被转发。对于Logger和Handler都拥有一个Level,但是Filter是可选的，可以设置也可以不设置。
# Level
Level中有七个日志级别，预设在Level的常量中，从低到高排列为
- **FINEST** (lowest value)
- **FINER**
- **FINE**
- **CONFIG**
- **INFO**
- **WARNING**
- **SEVERE** (highest value)  

<!-- more -->

除了这七个日志级别外还有两个常量**OFF**和**ALL**,OFF用来关闭日志记录，不接受任何日志消息，而ALL则是接收任何日志信息。当Logger和Handler没有设置Level的时候，其默认的日志级别为Level.INFO，任何低于该级别的消息都不会被转发和记录。

当LogRecord在Logger的分级命名空间上传播时，先看该消息是否符合记录它的Logger的日志等级，如果符合的话，还会被传播至其父级Logger上，此时会再次验证其是否符合父级设置的日志等级，如此在命名空间树上一级级传播，直到被某一级所拒绝。

# Filter
可以选择性的给Logger和Handler设置一个Filter，用来过滤所要记录的日志消息。Filter在java.util.logging包中是一个函数式接口，下面是它的类声明

```java
@FunctionalInterface
public interface Filter {
    public boolean isLoggable(LogRecord record);
}
```
isLoggalble()方法接收一个传递来需要验证LogRecord参数，该方法由我们自己实现一定的逻辑验证然后返回一个boolean类型参数，决定该LogRecord是否被发布。

需要注意的是Filter只会过滤一个Logger直接调用日志记录方法传递来的信息，也就是当一个Logger记录的日志信息向父级传播时，父级不会调用Filter来过滤消息。

