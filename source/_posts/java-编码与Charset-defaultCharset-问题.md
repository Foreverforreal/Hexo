title: java 编码与Charset.defaultCharset()问题
id: 1493895707044
author: 不识
tags:
  - java
categories:
  - java遇到的问题
date: 2017-05-04 19:01:00
---
学习I/O字符流的时候，使用FileReader读取一份txt文档，中文字符出现了乱码，这是读取文件的编码设置出了问题。学习时知道java字符流实际底层还是通过字节流，再由转换流来实现编码解码工作的，当我们没有设置字符编码时，转换流会自动获取默认的系统编码字符集。我们可以查看相关源码
```java
    public FileReader(String fileName) throws FileNotFoundException {
        super(new FileInputStream(fileName));
    }
```
<!-- more -->

FileReader调用父类初始化，而FileReader父类正是字节字符输入转换流InputStreamReader
```java
    public InputStreamReader(InputStream in) {
        super(in);
        try {
            sd = StreamDecoder.forInputStreamReader(in, this, (String)null); // ## 这里进行编码设置
        } catch (UnsupportedEncodingException e) {
            // The default encoding should always be available
            throw new Error(e);
        }
    }
```
继续跟进StreamDecoder.forInputStreamReader方法
```java
 public static StreamDecoder forInputStreamReader(InputStream var0, Object var1, String var2) throws UnsupportedEncodingException {
        String var3 = var2;
        if(var2 == null) {     //从上面调用知道var2为(String)null
            var3 = Charset.defaultCharset().name(); //这里获取默认字符集格式
        }

        try {
            if(Charset.isSupported(var3)) {
                return new StreamDecoder(var0, var1, Charset.forName(var3));
            }
        } catch (IllegalCharsetNameException var5) {
            ;
        }

        throw new UnsupportedEncodingException(var3);
    }
```
继续进入Charset.defaultCharset()

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

系统字符集的编码是根据我们设置的系统语言决定的，windows系统语言是中文时，默认字符集应该是GBK,创建的txt文档字符默认也是GBK格式的，按理来说我这里不应该出现乱码问题。    
实验一下，首先我将txt文档另存为UTF-8格式，再运行一次程序，乱码问题消失，这说明FileReader读取文件时使用的是UTF-8格式，用代码查看下
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

果然，java获取的并不是系统字符集，而很可能是IDE设置的字符编码，我是使用的intelliJ idea,在File→Settings→File Encodings→Project Encoding里将文件编码改为GBK,再次执行代码
>fileReader encoding is :GBK   
>systemEncoding is :GBK   
>defaultCharsetName is :GBK   

获取的编码也换成了GBK,读取的文档也没有乱码问题，就是这个原因了。
为了进一步确定是IDE对java的影响，先恢复IDE的字符编码设置为UTF-8，然后使用命令行javac编译，再运行一下class文件，命令行输出
>fileReader encoding is :GBK   
>systemEncoding is :GBK   
>defaultCharsetName is :GBK