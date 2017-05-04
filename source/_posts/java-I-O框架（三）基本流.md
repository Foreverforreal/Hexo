title: java I/O框架（三）基本流
id: 1493867604409
author: 不识
tags:
  - java
  - IO
categories:
  - java I/O框架
date: 2017-05-04 11:13:00
---

　　基本流有字节输入输出流(InputStream,OutputStream),和字符输入输出流(Reader,Writer),它们都是抽象类,作为Java I/O中其他所有字节字符流的父类存在.
　　我们知道计算机数据都是以二进制的形式存在,用字节(byte)形式就可以实现读写,在最初的JDK1.0版本中也只有字节流的存在,在之后的1.1版本才加入了字符输入输出流(Reader,Writer)。在Java中存储字符是使用的Unicode编码集

- **InputStream**
- **OutputStream**
- **Reader**
- **Writer**
  
<!-- more -->
# InputStream
![InputStream](\images\java io\InputStream.png)
　InputStream是java IO所有字节输入流的超类,如果你自己想要实现一个字节输入流组件,最好直接继承InputStream而不是他的子类,这样你可以处理所有类型的输入流.从上图中我们可以看到InputStream实现了Closeable接口,实际四个基本流都实现了此接口,其接口close()方法作为流操作结束时释放系统资源用.下面我们着重看InputStream的成员方法.

- **public abstract int read() throws IOException;**  
　　read()是一个抽象方法,由子类重写具体实现方式,它用来读取数据,且每次只读取一个字节,并以int类型返回该字节,当InputStream读取到文件末尾,再也没有数据时,则返回-1;  
  
- **public int read(byte b[]) throws IOException{...}**  
　　read(byte[])方法一次读取多个字节,并将其储存在传入的byte数组中,相比read(),其速度提高了很多.read(byte[])返回的是int类型所读取字节数,和read()一样,到文件末尾时,返回-1.  
  
- **public int read(byte[] b,int off,int len) throws IOException{...}**  
　　read(byte[],int,int) 与 read(byte[])类似,不同的是read(byte[] b ,int off,int len)可以通过传入参数off选择开始要写入到的数组索引位置,以及len确定要读取的字节数,具体细节可以看源码  
```java
public int read(byte b[], int off, int len) throws IOException {
         if (b == null) {     //确保传入要存储数据的数组不为空,否则抛出空指针异常
             throw new NullPointerException();
         } else if (off < 0 || len < 0 || len > b.length - off) {   //这里要判断传入的参数是否正常  其中 len > b.length - off是为了检查写入的字节数会不会超出数组索引
             throw new IndexOutOfBoundsException();
          } else if (len == 0) {
              return 0;
          }
 
         int c = read(); 
         if (c == -1) {   //判断读取文件是否为空
             return -1;
         }
         b[off] = (byte)c;  //将读入的第一个字节放入选择的数组索引off处
 
         int i = 1;     //变量i用来计实际读入数组的字节数
         try {
             for (; i < len ; i++) {
                 c = read();
                 if (c == -1) {
                     break;
                 }
                 b[off + i] = (byte)c;
             }
         } catch (IOException ee) {
         }
         return i;
     }
```

- **public long skip(long n) throws IOException{...}**    
　　skip(long)用来跳过不读一些字节数,InputStream定义了一个MAX\_SKIP_BUFFER_SIZE常量,大小为2048,这是一次所能跳过的最大字节数,其返回值为long类型的实际跳过字节数; 
  
- **public int available() throws IOException{...}**    
　　available()用来获取流中可读取数据大小,InputStream中此方法默认返回0,由其子类重写来实现具体方法;   
  
- **public void close() throws IOException{}**    
　　close()方法用来读取结束后,关闭流,释放系统资源,此方法一定要确保执行,否则会大量系统资源被占用得不到释放,会导致内存溢出.  
  
- **public boolean markSupported(){}**  
　　markSupported()是一个标记方法,用来表示是否支持mark(int)和reset()方法,InputStream 默认返回false,如果要想支持mark(int)和reset()方法,应当重写markSupport()使其返回true.  
  
- **public synchronized void mark(int readlimt) {}**  
　　mark(int)方法用来标记一个读取的文件字节位置,当执行reset()方法时,InputStream可以重新从此处读取,这适用于一些分析器,这些分析器有时需要预读后面一部分字节,如果没有发现目标,则返回重写读取.  
  
- **public synchronized void reset() throws IOException{}**  
　　reset()方法将流读取重置为mark(int)方法标记的位置  

# OutputStream
![OutputStream](\images\java io\OutputStream.png)

OutputStream是java IO所有字节输出流的超类,它实现了两个接口,Closeable和Flushable,其中Flushable接口的flush()方法是将缓冲区的数据直接刷入到输入流所要写入的对象,具体将在讲缓冲流的时候分析.下面我们看OutputStream的成员方法;

- **public abstract void write(int b) throws IOException;**  
　　write(int)方法用来写入一个字节的数据,但方法里传入的是一个int类型数据,不过这里只会写入int类型数据第一个字节,后面三个字节将被抛弃,也就是写入低8位，忽略高24位
  
- **public void write(byte b[]) throws IOException**  
　　write(byte[])方法是将一个数组的数据写入到字节输出流中
  
- **public void write(byte b[], int off, int len) throws IOException**   
　　和write(byte[])一样的用法,不同的是这里可以从数组指定索引处开始写入,并且可以设定要写入的字节长度,细节可以查看源码
    
- **public void flush() throws IOException {}**  
　　在使用缓冲流时,写入到流的数据并不会立即写入磁盘中,而是存在一个缓冲区,等到缓冲区满后才会执行实际写入,如果调用flush()方法,则立即执行将缓冲区数据写入到文件,flush()方法在OutputStream中方法体为空,需要子类重写去实现.　　
     
- **public void close() throws IOException {}**　  　　　　　
　　同InputStream
# Reader
![Reader](\images\java io\Reader.png)

Reader是java io中所有字符输入流的超类,它的大部分方法和InputStream相同,只不过Reader是以字符的形式读取数据,所以在一些需要人们阅读的文件读取上使用Reader.Reader相比InputStream多实现了一个Readerable接口,它使Reader可以将读取的数据存储在一个缓冲区内.而且Reader有两个构造方法,可以使用自己或者传入的对象作为同步锁,以保持读取同步,下面和InputStream相同的方法不再列举,可以自己参考InputStream.

- **public int read(java.nio.CharBuffer target) throws IOException {...}**

- **public int read() throws IOException {...}**
　　一次读取一个字符,并将该字符以int类型返回,当读到文件尾没有数据时,返回-1
  
- **public int read(char cbuf[]) throws IOException {...}**
　　一次读取多个字符,并将字符放到传入的字符数组中,返回值为实际读取的字符数,到文件尾没有数据时返回-1
  
- **abstract public int read(char cbuf[], int off, int len) throws IOException;**
　　一次读取多个字符,并将字符放入传入的字符数组中,并且可以指定传入字符数组索引和传入的字符数,返回值同样为实际读取字符数,到文件尾没有数据时返回-1

- **public boolean ready() throws IOException {...}**
　　ready()告诉程序字符流是否可以被读取,Reader中默认返回false

# Writer
![Writer](\images\java io\Writer.png)

Writer是java IO中所有字符输出流的超类,它的大部分方法和OutputStream一样,不同的是Writer写入的是字符.同Reader一样Writer有两个构造方法,可以使用自己或者传入的对象作为同步锁,以保证写入同步,,此外Writer还实现了一个Appenderable接口,可以追加写入字符