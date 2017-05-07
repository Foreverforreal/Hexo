title: java I/O框架 （七）缓冲流
id: 1494032990428
author: 不识
tags:
  - java
  - IO
categories:
  - java I/O框架
date: 2017-05-06 09:09:00
---
前面我们使用I/O都是无缓存的I/O。这样意味着每个读写请求都是直接直接由底层操作系统处理的。频繁的读写请求对于硬盘，和网络都是都有很大的性能消耗，为了减少这种支出，提高读写性能，Java实现了缓冲I/O流。

缓冲流对数据的读写都是间接通过内存里的一块缓冲区，缓冲输入流从内存缓冲区读取数据，只有当缓冲区清空时，本地输入API才会被调用，缓冲输出流将数据写入内存缓冲区，只有当缓冲区满时，才会调用本地输出API，将数据写入硬盘或网络传输。

<!-- more -->

缓冲流提供了四个类，分别是字节输入输出缓冲流BufferedInputStream,BufferedOutputStream和字符输入输出流：BufferReader,BufferedWriter.缓冲流是通过包装一些无缓冲的I/O流对象，为他们添加缓冲区，生成缓冲流对象，所有的缓冲流有相同形式的构造方法，以BufferedInputStream为例。
- public BufferedInputStream(InputStream in)
- public BufferedInputStream(InputStream in,int size)

两种构造函数中，第一种需要传入需要包装添加缓冲区的I/O流对象，第二种还可以传入一个int类型参数，用来指定缓冲区大小，当不指定时，缓冲流会有一个默认8192字节/字符的缓冲区，这已经足够大部分使用场景。

## 刷新缓冲区
当我们使用输出缓冲流的时候，写入的数据是先到内存缓冲区内，只有当缓冲区满时，才会执行实际写入。我们可以在比要的时候调用flush方法手动刷入缓冲区，flush在所有的输出流中都是可用的。
还有一些缓冲输出流是支持自动刷新的，只不过需要在构造函数内开启，这样在调用一些操作时，就会自动刷入缓冲区数据，比如PrintWriter可以在调用println或format方法时自动掉用flush.