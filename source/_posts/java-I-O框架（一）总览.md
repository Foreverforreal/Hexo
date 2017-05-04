title: java I/O框架（一）总览
id: 1493865226280
author: 不识
tags:
  - java
  - IO
categories:
  - java I/O框架
date: 2017-05-04 10:33:00
---
java io框架非常庞大,各种功能的类让人目不暇接,为了系统学习io框架,搜集了各种资料,整理出这篇文章,尽可能详细的讲述java io框架,其中会牵扯到许多信息,不仅包括框架内各种类的方法和使用对象,源码的解读(基于jdk1.8.0\_60),同时还会从整个框架层次,学习java io框架的设计模式和思想,坑挖的很大,慢慢填.引用的资料也会在后面全部列出;

<!-- more -->

# 概述  
　　一个I/O流是指一系列的数据，包含了一个输入的数据源和一个输出的目的地，而我们程序在其中起到一个操作中转的作用，input就是数据从数据源流向我们程序内存的过程，output则是从内存流向输出的目的地，可以用下图表示

![IO Stream](\images\java io\IO Stream.jpg)

　　数据源和输出目的地可以包含多种形式，下面是一些常见的形式。

- 文件(file)
- 网络(network)
- 内存缓存(Buffer)
- 线程内部通信(Pipe管道)
- 缓冲
- 过滤
- 解析
- 文本 (Readers / Writers Text)
- 基本类型数据 (long, int etc.)
- 对象(Object)

　　I/O流中传输的数据存在着两种基本的形式，一种是字节（byte）,一种是字符（char）,这样就组成了Java I/O框架四种基本流的形式。
- **InputStrem**
- **Reader**
- **OutputStream**
- **Writer**  
　　InputStream和OutputStream是字节输入输出流,Reader和Writer是字符输入输出流,I/O框架中其他字节字符流都是由这四种基本流派生而来。
 　 依照数据源、数据输出目的地与数据形式组合，Java I/O 中提供了一系列不同用途的通用实现类，下面表格中是这些通用实现的组合。
   
||Byte Based Input|Output|Character Based Input|Output|  
|--|---|---|--|---|
|**Basic**|InputStream|OutputStream|Reader InputStreamReader|Writer OutputStreamWriter|
|**Arrays**|ByteArrayInputStream|ByteArrayOutputStream|CharArrayReader|CharArrayWriter|
|**Files**|FileInputStream RandomAccessFile|FileOutputStream RandomAccessFile|FileReader|FileWriter|
|**Pipes**|PipedInputStream|PipedOutputStream|PipedReader|PipedWriter|
|**Buffering**|BufferedInputStream|BufferedOutputStream|BufferedReader|BufferedWriter|
|**Filtering**|FilterInputStream|FilterOutputStream|FilterReader|FilterWriter|
|**Parsing**|PushbackInputStream StreamTokenizer||PushbackReader LinenumberReader||
|**Strings**|||StringReader|StringWriter|
|**Data**|DataInputStream|DataOutputStream|||
|**Data-Formatted**||PrintStream||PrinrWriter|
|**Objects**|ObjectInputStream|ObjectOutputStream|||
 |**Utilities**|SequenceInputStream|||||

# 目录
 
1. 文件操作(File)
2. 基本流(InputStream,OutputStream,Reader,Writer)
3. 文件流(FileInputStream,FileOutputStream,FileReader,FileWriter,RadomAccessFile)
4. 转换流(InputStreamReader,OutputStreamWriter)
5. IO异常处理(IOException)
6. 缓冲流(BufferedInputStream,BufferedOutputStream,BufferdeReader,BufferedWriter)
7. 回退流(PushBackInputStream,PushBackReader)
8. 管道流(PipeInputStream,PipeOutputStream,PipeReader,PipeWriter)
3. 数组流(ByteArrayInputStream,ByteArrayOutputStream,CharArrayReader,CharArrayWriter)
10. 打印流(PrinteStream,PrintWriter)
11. 对象流(ObjectInputStream,ObjectOutputStream)
12. 字符串流(StringReader,StringWriter)
13. 数据流(DataInputStream,DataOutputStream)
14. 其他流(StreamTokenizer,LineNumberReader,SequenceInputStream)
15. System.in和System.out 以及System.error
15. 序列化(Serializable)
16. Java IO与装饰者模式
17. java NIO

# 参考
>[《Java IO教程》   --------  作者：Jakob Jenkov](http://tutorials.jenkov.com/java-io/outputstream.html)  
>[Java IO教程 系列  --------  skywang12345](http://www.cnblogs.com/skywang12345/p/io_01.html)  
>[从Decorator，Adapter模式看Java/IO库（一）    --------  lin_bei ](http://blog.csdn.net/lin_bei/article/details/1067506) 
>[关于inputStream.available()方法获取下载文件的总大小 - 无知者无畏 - ITeye技术网站       -------- hold_on ](http://hold-on.iteye.com/blog/1017449) 
