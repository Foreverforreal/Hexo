title: java 日志
id: 1495605524699
author: 不识
date: 2017-05-24 14:02:54
tags:
---
# 控制流程概述

应用程序在Logger对象上进行日志记录调用。Logger以分层式命名空间组织并且自Logger可能会在命名空间中的父Logger继承一些日志记录属性.

Logger对象分配LogRecord对象，LogReocord对象是被传递给Handler对象进行发布。Logger和Handler都有可能使用日记记录的Level和(可选的)Filter来决定是否对特定的LogRecord 感兴趣。当有必要从外部发布一个LogRecord时，一个Handler可以（可选地）在将其发布到一个I/O流之前，使用Formatter来本地化和格式化该消息。
![logging flow](/images/java base/logging1.gif)
<!-- more -->
每个Logger跟踪一组输出Handlers。默认的，所有的Logger同样发送它们的输出到它们的父Logger中。但是Logger也可能被配置为忽略树状结构上的Handler。

一些Handler可能直接输出到其他的Handler上。比如MemoryHandler维持一个LogRecord的内部循环缓冲，并且被事件触发时它通过一个目标Handler来发布它的LogRecords。在这种情况下，任何格式化由链上的最后一个Handler来完成
![MemoryHandler](/images/java base/logging2.gif)
这样的API结构可以使当日记被禁用时，能够轻易的来调用Logger API。如果对于给定的日志级别禁用日志记录，则Logger可以进行轻易的进行比较测试并返回。如果对于给定的日志级别禁用日志记录，在将LogRecord传递到处理程序之前，Logger仍然小心的降低成本开销。特别的，本地化和格式化（它们相对开销更大）被推迟到Handler请求处理它们时。比如MemoryHandler能够维持LogRecord的循环缓冲区，而不必支付格式化的开销。

# 日志级别
每条日志信息都有一个关联的日志Level。Level对日志消息的重要性和紧迫性进行了大概的描述。日志Level对象封装了一个整数值，更高的值表示了更高的优先度。

Level类定义了七个标准日志级别，范围从FINEST(最低的优先度，最低的整数值)到SEVERE（最高的有限度，最高的值）

Logger可以设置一个最小的日志级别，它决定是否将消息转发到处理程序。这不是使用Filter，即使它具有相同的效果。这样，可以抑制低于一定级别的所有消息。

# Loggers

像前面所说的，客户端代码要发送日志请求到Logger对象中。每个Logger跟踪其感兴趣的日志级别，并抛弃低于此级别的日志请求。

Logger通常是命名实体，使用点分隔的名称，如“java.awt”。命名空间是分层式的并且由LogManager来管理。命名空间通常应与Java包装命名空间对齐，但不需要严格遵守。例如，名为“java.awt”的Logger可能会处理java.awt包中的类的记录请求，但它也可能会处理sun.awt中的类的日志记录，该类支持java.awt包中定义的客户端可见抽象。

除了命名为Logger外，也可能创建一个不在共享命名空间中的匿名Logger，可以看章节1.14。

Logger在日志命名空间中保持追踪他们父logger。logger的父级是日志记录命名空间中最近的祖先。根Logger(称为“”)没有父级。匿名logger都将根logger作为他们的父级。logger可能从他们的父级继承多种属性在日志命名空间内。特别的，一个looger可能继承一下属性

- 日志级别。如果一个Logger的级别被设置为null,那么这个Logger会使用从遍历父级树并且使用第一个非null的Level作为其有效Level。
- Handler。默认情况下，Logger会将任何输出消息记录到其父级的handler中，如此在在树状上递归传递。
- 资源束名称（Resource bundle names）。如果记录器具有空资源束名称，那么它将继承任何为其父级定义的资源束名称，以及递归上传树。

# 日志方法

Logger类为生成日志消息提供了一大堆方便的方法。为方便起见，每个日志记录级别都有以日志记录级别名称命名的方法。因此相比调用“logger.log(Level.WARNING)”一个开发者可能会简单的调用更便易的方法“logger.warning(..)”

有两种不同风格的日志记录方法，以满足不同社区的用户需求。

第一种，这些方法接收一个明确的资源类名和资源方法名。这种方法适用于希望能够快速找到任何给定日志记录消息源的开发人员。如下
```java
void warning(String sourceClass, String sourceMethod, String msg);
```
第二种，这类方法不接受明确的资源类和资源方法。这些适用于希望易于使用的日志记录并且不需要详细的源信息的开发人员。

```java
void warning(String msg);
```

对于第二种方法，日志框架将作出最大努力来确定调用到日志框架中的类和方法，并将此信息添加到LogRecord中。但是需要意识到这个自动推断信息可能只是近似的。最新一代的虚拟机在JITing时会进行广泛的优化，并且可能会完全删除堆栈帧，从而无法肯靠地定位调用类和方法。

# Handlers

Java SE提供一下几种Handlers:

- StreamHandler:一个用于将格式化记录信息写入到OutputStream的简单的handler;
- ConsoleHandler:一个用于将格式化记录信息写入到System.err的简单的handler;
- FileHandler:一个用于将格式化日志记录信息写入到单个文件，或者一系列日志记录文件的handler。
- SocketHanler:一个用于将格式化日志记录信息写入到远程的TCP端口的handler.
- MemoryHandler:一个缓存日志记录信息到内存的handler。

开发一个新的Handler是相当直接的。需要特定功能的开发者可以从头开发一个Handler，也可以从继承自以上提供的Handler的子类进行开发。

# Formatters
Java SE 同样包括两种标准的Formatter。
- SimpleFormatter:写出简洁易读的日志记录摘要。
- XMLFormatter:写详细的XML结构化信息。

与Handlers一样，开发新的Formatter是相当直接的。

# LogManager
有一个全局LogManager对象跟踪全局日志记录信息。这包括：

- 命名logger的分级命名空间。
- 从配置文件读取的一组日志记录控制属性。见1.8节。

有一个可以使用静态LogManager.getLogManager方法获取的LogManager对象。这是在LogManager初始化期间根据系统属性创建的。此属性允许容器应用程序（如EJB容器）用自己的LogManager子类代替默认类。

# 配置文件

可以通过使用一个在启动时读取的日志配置文件来初始化日志配置。此日志记录配置文件采用标准的java.util.Properties格式。

或者，可以通过指定可用于读取初始化属性的类来初始化日志记录配置。这种极值允许可以从任意资源比如配置数据LDAP，JDBC等等中读取配置数据。详细的请看 LogManager API Specification。

有一小组全局配置信息。这在LogManager类的描述中指定，并包括在启动期间安装的根级别Handler的列表。

初始配置可以指定特定logger的日志级别。这些日志级别应用于命名的logger以及该命名层之下的所有logger。这些日志级别按照它们在配置文件中定义的顺序应用。

初始配置可能包含任意属性，供Handerl或子系统进行日志记录使用。按照惯例，这些属性应该使用以handler类名或子系统的主Logger的名称开头的名称。

比如，MemoryHandler使用一个属性“java.util.logging.MemoryHandler.size”来决定它的循环缓冲区的大小。

# 默认配置
JRE附带的默认日志记录配置只是默认的，可以被ISV，系统管理员和最终用户覆盖。

默认配置只是限制使用的磁盘空间。它不会向用户灌输信息，而是确保始终捕获关键故障信息。

默认配置在根logger上建立一个单独的handler，用于将输出发送到控制台。

# 动态配置更新

程序员可以通过多种方式在运行时更新日志记录配置：

- FileHandlers，MemoryHandler和PrintHandler都可以使用各种属性创建。
- 可以添加新的Handlers ，并删除旧Handlers 。
- 可以创建新的Loggers ，并可以提供特定的Handlers。
- 可以在目标Handler上设置Level.

# 本地方法

日志中没有本地API.
希望使用Java Logging机制的本地代码应该使Java Logging API正常进行JNI调用。

# XML DTD
XMLFormatter使用的XML DTD在附录A中指定。
DTD设计有一个“&lt;log>”元素作为顶级文档。然后每个日志记录写作“&lt;record>”元素的形式。
请注意，在JVM崩溃的情况下，可能无法使用适当的关闭</ log>清理终止XMLFormatter流。因此，分析日志记录的工具应该准备好应对未终止的流。

# 唯一信息ID

Java日志API不提供任何对唯一信息ID的直接支持。那些需要唯一消息ID的应用程序或子系统应该定义自己的惯例，并在消息字符串中适当地写入唯一的ID。

# 安全性

主要的安全性要求是不受信任的代码不能更改日志记录的配置。具体来说，如果日志配置已设置为将特定类别的信息记录到特定的Handler中，则不受信任的代码不应该能够阻止或中断该日志记录。

新的安全权限LoggingPermission被定义用来控制日志配置的更新。

受信任的应用程序将提供适当的LoggingPermission，以便它们可以调用任何日志记录配置API。不信任的程序就是另一回事了。不受信任的小应用程序可以以常规方式创建和使用命名logger，但不允许更改日志记录控制设置，例如添加或删除处理程序或更改日志级别。但是，不信任的applet可以使用Logger.getAnonymousLogger来创建和使用自己的“匿名”记录器。这些匿名记录器未在全局命名空间中注册，并且它们的方法不进行访问检查，甚至允许不可信代码更改其日志控制设置。

日志框架不会阻止欺骗。日志记录调用的源头不能被可靠地确定，因此当发布声明来自特定源类和源方法的LogRecord时，它可能是伪造的。类似地，诸如XMLFormatter之类的格式化程序不会尝试保护自身免受消息字符串内的嵌套日志消息的影响。因此，欺骗性LogRecord可能在其消息字符串中包含一组恶意的XML，以使其看起来像输出中有一个额外的XML记录。

此外，日志框架不会尝试保护自身免遭拒绝服务攻击。任何给定的日志客户端都可以使用无意义的消息来淹没日志框架，以试图隐藏一些重要的日志消息。

# 配置管理
API使得初始配置信息被一属性的形式从一个配置文件中读取。这些配置信息可以通过调用各种日志类和对象来被编程式修改。

另外，LogManager上还有一些可以重新读取配置文件的方法。发生这种情况时，配置文件的属性值将覆盖已经以编程方式进行的任何更改。

# 包
所有的日志记录类都在java.util.logging包中的java.\*部分命名空间中。

# 本地化
日志消息可能需要本地化
每个Logger可能具有与其关联的资源包名称。相应的资源包可用于在原始消息字符串和本地化消息字符串之间映射。

通常，本地化将由Formatters执行。为了方便起见，formatter类提供了一种formatMessage方法，它提供了一些基本的本地化和格式化支持。

# 远程访问和序列化

与大多数Java平台API一样，日志API旨在在单个地址空间内使用。所有的调用都是本地的。但是预计一些Handler希望将输出转发到其他的系统。这里有各种方式来实现这一点。
一些Handler(比如SocketHandler)可能会使用XMLFormatter来将数据写入到其他系统。这提供了可以在各种系统上解析和处理的简单，标准的互换格式。
一些Handler可能希望通过RMI传递LogRecord对象。因此LogRecord类是可序列化的。但是，这有一个问题是如何处理LogRecord参数。一些参数可能不是可序列化的，并且其他参数可能被设计为序列化比日志记录要求更多的状态。为了避免这个问题,LogRecord类有一个自定义的wirteObject方法，在将它们写入之前将参数转换为字符串（使用Object.toString（））。
大多数日志记录类不是没有必要序列化的。
Loggers 和Handlers 都是绑定到特定虚拟机中的状态类。在这方面，它们类似于java.io类，它们也不是可序列化的。