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
- **InputStreamReader**
- **OutputStreamWriter**

这两个转换流分别直接继承自Reader和Writer,他们的构造函数提供一个包装器功能，传入一个相应的字节流，使用这个底层的字节流进行相应的读取写入工作，然后转换流附加上字符的解码编码功能。

InputStreamReader是字节通向字符的桥梁，它读取字节并通过给定的字符集将字节解码为字符，而OutputStreamWriter则相反，它是字符通向字节的桥梁，将字符以给定的字符集进行编码，用字节的形式写入。字符集我们自己可以指定，不指定的话则由转换流自动获取系统使用的默认字符集。

<!-- more -->


# 构造方法
InputStreamReader和OutputStreamWriter构造函数形式一致，以InputStreamReader为例

|构造函数|意义|
|--------|----|
|InputStreamReader(InputStream in)|使用默认字符集新建一个InputStreamReader|
|InputStreamReader(InputStream in, String charsetName)|使用给定字符集名字新建一个InputStreamReader|
|InputStreamReader(InputStream in, Charset cs)|使用给定字符集新建一InputStreamReader|
|InputStream in, CharsetDecoder dec|使用给定字符解码器新建一InputStreamReader|

使用转换流的时候我们需要给定一个正确的字符集，否则可能出现乱码现象，在第二种构造方法中如果给的字符集名称不存在的话，会抛出UnsupportedEncodingException: XXX异常。

# 普通方法
转换流都和字符编码有关，所以都有一个getEncodeing()方法，用来获取当前流要使用的字符集编码形式。转换流是包装的一个普通字节流，我们知道I/O流使用结束后都需要关闭的，但是使用转换流的时候只需要调用转换流close()方法，它会自动关闭内部的字节流，不需要我们再去关闭。其他方法则和其父类用法相同。

## InputStreamReader

|修饰符和类型|方法|描述|
|------------|----|----|
|void|**close**()|关闭流，并释放相应的系统资源|
|String|**getEncoding**()|获取流使用的字符编码名字|
|int|**read**()|读取一个字符|
|int|**read**(char[] cbuf,int offset,int length)|将字符读入到给定的数组中一部分|
|boolean|**ready**()|告诉流是否准备好读取操作|

# OutputStreamWriter

|修饰符和类型|方法|描述|
|------------|----|----|
|Writer|**append**(CharSequence csq)|在这个wirter后追加一个指定字符|
|Writer|**append**(CharSequence csq, int start, int end)|在这个wirter后追加一个指定字符序列的子序列|
|void|**close**()|先刷新缓冲区数据，再关闭流|
|void|**flush**()|刷新流|
|String|**getEncoding**()|获取流使用的字符编码名字|
|void|**write**(char[] cbuf,int off,int len)|将一个字符数组的一部分写入|
|void|**write**(int c)|写入一个字符|
|void|**write**(String str,ing off,int len)|将给定的字符串中一部分写入|