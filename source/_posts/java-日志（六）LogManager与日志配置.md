title: java 日志（六）LogManager与日志配置
id: 1495800760990
author: 不识
tags:
  - java
  - logging
categories:
  - java 基础
  - java 日志
date: 2017-05-26 20:12:42
---
***
# LogManager
***
## 属性配置
***
LogManager用来管理Java Logging API的配置，在整个JVM运行期间只存在一个LogManager实例，使用它的静态方法来获取该实例。
```java
LogManager manager = LogManager.getLogManager();
```
LogManager是随着虚拟机的启动而进行初始化，在这期间它通过readConfiguration()方法初始化日志记录配置。缺省情况下，JVM使用LogManager默认配置。在启动虚拟机后，如果想要重新加载配置文件，可以使用一下两个方法
- readConfiguration()
- readConfiguration(inputStream)

<!-- more-->
第一种是再次读取配置类或配置文件，防止在虚拟机启动后这些配置信息发生改变。第二种使用输入流来重设日志配置。
如果我们要想使用自己的日志管理类，在虚拟机启动时通过java.util.logging.manager参数指定要使用的manager。

## 命名空间管理
***
LogMananger管理着命名空间中的所有命名Logger对象和对象的属性设置，LogManager也提供方法来获取这些被管理的对象和属性。以下是主要获取方法。  
### 对象管理
- addLogger(Logger logger)
- getLogger(String name)
- getLoggerNames()

addLogger()是向命名空间内新添加一个Logger对象，并返回一个boolean值表示是否添加成功，如果该命名Logger已经存在的话，就会添加失败返回false。getLogger()依据命名获取特定的Logger对象，getLoggerNames()获取获取已知Logger名称的枚举。

### 属性管理
- getProperty(String name)
- reset()

getProperty()可以依据属性名，获取读取的配置属性的值。reset()方法会重置所有的日志配置，对于所有的命名Logger,reset()会移除并关闭它的所有Hanlder,并将其Level设置为null,对于根Logger,它的Level会被设置为Level.INFO。

>LogManager所有的方法都是线程安全的。
***
# 日志配置
***
Java Logging API可以通过两种方式进行配置：
1. 通过配置类
2. 通过配置文件

这两种配置方式分别是通过一个java类或property文件来存储日志配置的属性键值对。配置的初始化由java.util.logging.LogManager类负责，而LogManager在JVM启动时就会根据系统属性初始化，并且在初始化的时候会读取日志配置属性，所以我们需要在要启动JVM时，通过命令行来指定这两种配置方式属性。下面分别是配置类和配置文件的JVM属性

- java.util.logging.config.class
- java.util.logging.config.file

在属性后加类或文件的路径信息，如“java.util.logging.config.class=com.config.LoggerConfig”

## 配置类
***
您可以使用Java类来配置Java Logging API。您可以通过在JVM参数java.util.logging.config.class中指定类的名称来实现。该类的构造函数应该加载日志配置并将其应用于分层命名空间中的Logger。

## 配置文件
***
不使用配置类，也可以使用配置文件来配置日志记录。Java Logging API中有一个默认配置文件，路径为JRE目录下的“lib/logging.properties”，如果编辑该文件的话，就改变了整个JRE的默认日志配置，这会影响所有的运行程序。
通常我们会为应用程序建立一个单独的配置文件，并且可以通过将JVM属性java.util.logging.config.file设置为指向此文件来实现。

在写配置文件的时候需要注意，配置属性按照它们在配置文件中列出的顺序应用的，也就是说，应当先配置父Logger的属性，然后再配置子Logger,否则，父Logger的配置将覆盖子Logger的配置。