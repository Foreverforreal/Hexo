title: java I/O框架（一）总览
id: 1493865226280
author: 不识
tags:
  - 'java '
  - IO
categories:
  - java I/O框架
date: 2017-05-04 10:33:00
---

　　java io框架非常庞大,各种功能的类让人目不暇接,为了系统学习io框架,搜集了各种资料,整理出这篇文章,尽可能详细的讲述java io框架,其中会牵扯到许多信息,不仅包括框架内各种类的方法和使用对象,源码的解读(基于jdk1.8.0\_60),同时还会从整个框架层次,学习java io框架的设计模式和思想,坑挖的很大,慢慢填.引用的资料也会在后面全部列出;

<!-- more -->

# 概述  
　　io流操作是java io的核心,io的是input/output的缩写,意思是输入与输出,我们以运行的java程序为主体,input就是数据流向程序内存,output就是指数据从程序内存流出,可以用下图表示

![IO Stream](\images\java io\IO Stream.jpg)

　　(1) 从上图可以看出,io流就是数据从Source流向Destination的一个过程,而数据流的Sources和Destination我们可以概括为以下几种

- 文件访问(file)
- 网络访问
- 内存缓存访问(Buffer)
- 线程内部通信(Pipe管道)
- 缓冲
- 过滤
- 解析
- 读写文本 (Readers / Writers Text)
- 读写基本类型数据 (long, int etc.)
- 读写对象(Object)

　　(2) 当我们从读取或者写入文件时,数据是以字节(byte)或字符(char)的形式流动,依据不同形式读写的流,,java io中设计了四个类

- **InputStrem**
- **Reader**
- **OutputStream**
- **Writer**
　　InputStream和OutputStream是字节的输入和输出,Reader和Writer是字符的输入和输出,java io中其他各种各样的类均是由这四种类派生而来
 　  Java I/O框架根据io流针对的对象以及流的形式,设计了不同功能的类,我们可以在下表中查看,这些类也是我们重点学习的对象


||Byte Based Input|Output|Character Based Input|Output|  
|--|---|---|--|---|
|**Basic**|InputStream|OutputStream|Reader InputStreamReader|Writer OuputStreamWriter|
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

# 文章目录
 下面我们也根据这个表,来学习java中各种流操作,以及一些和io相关的东西

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