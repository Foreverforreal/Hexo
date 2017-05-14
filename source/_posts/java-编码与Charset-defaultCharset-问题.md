title: java 编码与Charset.defaultCharset()问题
id: 1493895707044
author: 不识
tags:
  - java
categories:
  - java遇到的问题
date: 2017-05-04 19:01:00
---


　　在学习I/O字符流的时候，使用FileReader读取一份txt文档时，中文字符显示出现了乱码问题。Java中字符流实际底层实际还是使用字节流，再由转换流来实现编码解码工作的。当我们没有设置字符编码时，转换流会自动获取默认的系统编码字符集。
具体我们可以查看相关源码，从FileReader的初始化开始，下面是它的构造方法。
```java
    public FileReader(String fileName) throws FileNotFoundException {
        super(new FileInputStream(fileName));
    }
```
<!-- more -->

可以发现FileReader初始化时使用super(new FileInputStream(fileName))调用父类构造方法，而FileReader父类正是字节字符输入转换流InputStreamReader，我们进入InputStreamReader查看其被调用的构造方法
```java
    public InputStreamReader(InputStream in) {
        super(in);
        try {
            sd = StreamDecoder.forInputStreamReader(in, this, (String)null); // 编码是在这里进行设置，最后一个参数指定字符集，但这里传入了一个null
        } catch (UnsupportedEncodingException e) {
            throw new Error(e);
        }
    }
```
继续跟进StreamDecoder.forInputStreamReader方法
```java
 public static StreamDecoder forInputStreamReader(InputStream var0, Object var1, String var2) throws UnsupportedEncodingException {
        String var3 = var2;     // var2参数为字符集的名称
        if(var2 == null) {     //从传入参数知道var2为(String)null
            var3 = Charset.defaultCharset().name(); //这里获取默认字符集名称
        }
       ...
       ...
    }
```
上面在Charset.defaultCharset()获取使用的字符集名称，我们进入该方法

```java
        if (defaultCharset == null) {
            synchronized (Charset.class) {
                String csn = AccessController.doPrivileged(
                    new GetPropertyAction("file.encoding"));//file.encoding正是系统文件字符集的配置名称啦
                Charset cs = lookup(csn);
                if (cs != null)
                    defaultCharset = cs;
                else
                    defaultCharset = forName("UTF-8");
            }
        }
        return defaultCharset;
```
可以看出当没有设置字符集名称时，在AccessController.doPrivileged(new GetPropertyAction("file.encoding"));中会去获取我们操作系统使用的字符集，来作为转换流的编码解码格式。

而系统字符集的编码是根据我们设置的系统语言决定的，windows系统语言是中文时，默认字符集应该是GBK,创建的txt文档字符默认也是GBK格式的，按理来说我这里不应该出现乱码问题。    
于是将txt文档另存为UTF-8格式，再运行一次程序，乱码问题消失，这说明FileReader读取文件时使用的是UTF-8格式，用代码获取字符流使用编码格式，以及Charset使用的字符集，和当前系统使用字符集，它们是一致的。
```java
System.out.println("fileReader encoding is :"+fileReader.getEncoding());

String defaultCharsetName= Charset.defaultCharset().displayName();
String systemEncoding= System.getProperties().get("file.encoding").toString();

System.out.println("systemEncoding is :"+systemEncoding);
System.out.println("defaultCharsetName is :"+defaultCharsetName);

```
控制台输出
>fileReader encoding is :UTF8  
>systemEncoding is :UTF-8  
>defaultCharsetName is :UTF-8  

显示字符集使用的都是UTF-8，并不是我当前操作系统字的GBK符集，而很可能是IDE设置的字符编码，我是使用的intelliJ idea,在File→Settings→File Encodings→Project Encoding里将文件编码改为GBK,再次执行代码
>fileReader encoding is :GBK   
>systemEncoding is :GBK   
>defaultCharsetName is :GBK  

此时乱码问题消失，并且可见IDE会影响JVM获取系统的字符集，在idea的run窗口运行程序时会显示其编译运行命令的一些参数如图
![run window](\images\other\idea run.png)
注意标红区域，这里idea在使用jdk时会事先设置了字符编码格式，当我们使用java获取系统编码时，获取的只是idea设置的编码格式。如果想看不在idea运行结果，我们可以命令行编译运行一下代码，命令行输出
>fileReader encoding is :GBK   
>systemEncoding is :GBK   
>defaultCharsetName is :GBK

显示的是系统使用的字符集。


