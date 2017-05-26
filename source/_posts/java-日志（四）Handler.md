title: java 日志（四）Handler
id: 1495789985590
author: 不识
date: 2017-05-26 17:13:06
tags:
  - java
  - logging
categories:
  - java 基础
  - java 日志
---
Handler是负责实际将记录消息发布到外部的组件。我们可以在一个Logger上添加多个Hanlder,实现日志信息发送到不同的目的地。
# Handler设置方法

　　Handler和Logger一样拥有Level和Filter，此外由于Handler负责实际发布消息到外部，它还有一个负责将字符格式化的Formatter,对此Handler有一系列的设置方法  
<!-- more -->
- setFilter(Filter newFilter) 
- getFilter() 
- setLevel(Level newLevel) 
- getLevel() 
- setFormatter(Formatter newFormatter) 
- getFormatter() 


# 内置Handler
　　java.util.logging提供了Handler接口，但是也为我们提供了几个内置的Handler：
- **Handler**
	- **StreamHandler**
     - **ConsoleHandler**
     - **FileHandler**
     - **SocketHandler**
	- **MemoryHandler**
    
## StreamHandler 

　　StreamHandler会将日志消息写到一个OutputStream中，它有两种构造方法可供初始化一个StreamHandler

>StreamHandler()  
>StreamHandler(OutputStream out,Formatter formatter)

　　第一种为无参构造，没有指定日志消息要写入的OutputStream和要使用的Formatter，此时可以不用再设定Formatter,因为StreamHanderl会使用一个默认的SimpleFormatter，但还需要调用StreamHandler的setOutputStream(OutputStream)来指定一个输出流。   
　　第二种构造器接收一个OutputStream和Formatter,这时setOutputStream(OutputStream)方法可以用来更改原有的输出流。

## ConsoleHandler
　　ConsoleHanderl将所有的日志信息输出到System.err中，它默认使用SimpleFormatter来格式化日志信息。ConsoleHandler只用一个无参构造。

## FileHandler
　　FiletHandler将所有的日志信息写入到文件中，既可以是一个单独文件，也可以是一系列的滚动记录文件。如果采用滚动文件的形式，需要指定每个文件的大小限制，达到该限制后就会创建一个新的记录文件，并且还需要指定文件的命名形式，每个文件名由基本名称和序列号组成。比如mylog.0.txt, mylog.1.txt 等等。
　　FileHanderl默认使用XMLFormatter来格式化日志信息。下面是FileHandler的所有构造方法。

### 构造方法
>FileHandler()  
>FileHandler(String pattern)  
>FileHandler(String pattern, boolean append)  
>FileHandler(String pattern, int limit, int count)  
>FileHandler(String pattern, int limit, int count, boolean append)

　　第一种构造方法是无参构造，使用这种构造方法，将完全由LogManager读取的配置文件属性（或其默认值）来配置。
　　第二个构造方法创建一个具有预定义模式的FileHandler，用于生成日志文件的文件名。 
　　第三个构造方法使用一个文件名模式和一boolean值来创建一个FileHandler,这里没有指定最大日志文件数，其默认为1，也就是只会使用一个日志文件，boolean值参数决定FileHandler是每次追加写入原有的日志文件，还是覆盖写入。  
　　第四个构造方法使用一个文件名模式，一个文件大小限制，一个最大文件数来创建一个FileHandler,当日志文件达到大小限制后，就会新建一个日志文件，如此一直达到最大文件数后，会删除最早的文件继续记录。  
　　第五个构造方法相比第四个，只是多了一个boolean值，用来决定FileHandler是否应该追加写入到已有的日志文件。
  
### 文件名模式
　　文件名的模式是通过字符串来表示的，它使用一些字符来告诉FileHandler生成文件名的方式

|字符|意义|
|----|----|
|**/**|系统文件分隔符，通常是\或者/|
|**%t**|系统的临时目录|
|**%h**|系统的用户主目录|
|**%g**|将滚动日志文件彼此区分开的代号|
|**%u**|避免命名冲突的唯一编号|
|**%%**|单个百分号，%的转义字符|

　　如果命名模式中没有指定％g，并且FileHandler的文件数大于1，则会将文件序列号直接添加到文件名后，并且以一个“.”分割开。
　　%u是用来避免文件命名冲突的，当同时有两个程序运行的时候。%u通常被设置为0，当产生的日志文件名已经存在的时候，会依照数字顺序增加1，知道产生一个不会冲突的文件名。如果命名模式中没有指定%u,而文件名又发生冲突的话，会直接在文件名后添加不冲突的序号。
>%u只保证在本机文件系统上才会添加唯一序列号。

　　这举一个例子表示，文件命名模式字符串为“logfile.%g%u.txt”，则滚动产生的文件名为logfile0.0.txt, logfile.1.0txt，logfile.2.0txt.....此时如果logfile.3.0txt文件名已经存在，则会使用logfile.3.1txt。

## SocketHandler
　　SocketHandler会通过一个socket将日志信息发送出去，默认使用XMLFormatter进行日志信息格式化。它有两种构造方法
>SocketHandler()
>SocketHandler(String host, int port)

　　第一种构造方法只能使用LogManager从配置文件读取的属性来配置。
　　第二种构造方法指定了要日志消息发送到的主机名和端口号。
　　SocketHandler默认使用XMLFormatter进行日志信息格式化。

## MemoryHandler

　　MemoryHandler是一个将LogRecords内部的保留在缓冲区中的handler。当内部缓冲区已满时，新的LogRecords开始覆盖缓冲区中最旧的部分。  
　　当发生某个触发事件时，内部缓冲区中的LogRecord将被刷新到目标Handler，该Handler将将LogRecords写入外部系统。例如，当一个最小日志级别的LogRecord被记录时，就可以刷出LogRecord的整个缓冲区。此外我们也可以调用它的push()方法强制刷出缓冲区。
　　MemoryHandler也有两种构造方法
>MemoryHandler()
>MemoryHandler(Handler target, int size, Level pushLevel)

　　第一种是无参构造，需要根据LogManager配置属性进行配置。
　　第二种构造方法指定了目标Handler,缓冲区大小，以及最小刷出缓冲区的日志级别

>需要注意的是MemoryHandler没有Formatter，它不会进行格式化工作。