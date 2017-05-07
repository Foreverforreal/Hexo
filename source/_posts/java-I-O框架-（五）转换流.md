title: java I/O框架 （五）转换流
id: 1494072459566
author: 不识
tags:
  - java
  - IO
categories:
  - java I/O框架
date: 2017-05-06 20:08:00
---


转换流是字节流与字符流之间的桥梁，转换流有两个
- InputStreamReader
- OutputStreamWriter

这两个转换流分别直接继承自Reader和Writer,他们的构造函数提供一个包装器功能，传入一个相应的字节流，使用这个底层的字节流进行相应的读取写入工作，然后转换流附加上字符的解码编码功能。

InputStreamReader是字节通向字符的桥梁，它读取字节并通过给定的字符集将字节解码为字符，而相反的OutputStreamWriter是字符通向字节的桥梁，它将字符以给定的字符集进行编码，用字节的形式写入。字符集可以自己给定，或者由转换流自动获取系统使用的默认字符集。



# 构造函数
InputStreamReader和OutputStreamWriter构造函数形式一致，以InputStreamReader为例
|构造函数|意义|
|--------|----|
|InputStreamReader(InputStream in)|使用默认字符集新建一个InputStreamReader|
|InputStreamReader(InputStream in, String charsetName)|使用给定字符集|
|InputStreamReader(InputStream in, Charset cs)||
|InputStream in, CharsetDecoder dec||
