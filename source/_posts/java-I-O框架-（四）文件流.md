title: java I/O框架（四）文件流
id: 1493868657034
author: 不识
tags:
  - java
  - IO
categories:
  - java I/O框架
date: 2017-05-04 11:30:00
---
# 文件读取

　　FileInputStream和FileReader是文件字节输入流和文件字符输入流,都是提供文件读取功能,只是读取形式不同,一个以字节为最小单位读取,一个以字符为最小单位读取,并且两者都必须从文件头开始,按顺序读取,读取结束后需要调用close()方法释放流对象.  
>两个文件输入流都不支持mark(int),reset()操作，其markSupported()方法都返回false

两个文件输入流的有参构造方法一致,以FileInputStream为例

- public FileInputStream(String name)  
- public FileInputStream(File file)      
- public FileInputStream(FileDesciptor fd)

<!-- more -->

## FileInputStream

　　FileInputStream直接继承自InputStream

```java
package com.java.io;

import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;

/**
 * 在桌面创建一个input.txt文本,内容为"The quick brown fox jumps over a lazy dog.",文件大小是42字节,
 * 下面分别测试FileInputStream成员方法
 */
public class FileInputStreamTest {
    private final static String filePath = "C:\\Users\\Administrator\\Desktop\\input.txt";

    //使用read()方法单字节读取
    public static void readCase() throws IOException {
        InputStream input = new FileInputStream(filePath);

        int data;
        while ((data = input.read()) != -1) {
            System.out.print((char) data);
        }
        input.close();
    }

    //使用read(byte[] b)方法使用数组读取,read(byte[] b,int off,int len)相同
    public static void readByteArrayCase() throws IOException {
        InputStream input = new FileInputStream(filePath);

        byte[] bytes = new byte[10];
        int data = input.read(bytes);
        while (data != -1) {
            System.out.print(new String(bytes,0,data));   //每次读入到bytes数组中时,会按索引重复覆盖数据,
            data=input.read(bytes);                       //最后一次读入可能会残留上一次的数据
        }
        input.close();
    }
    //测试skip(int)方法,使流可以跳过一定字节数据,最大跳过字节数为2048
    public static void skipCase() throws IOException{
        InputStream input=new FileInputStream(filePath);
        input.skip(4);                                     //跳过四个字节

        int data=input.read();
        while(data!=-1){
            System.out.print((char)data);
            data=input.read();
        }
        input.close();
    }
    //测试available()方法,获取文件的大小
    public static void availableCase() throws IOException{
        InputStream input=new FileInputStream(filePath);
        System.out.print(input.available());
        input.close();
    }
    //测试markSupported()等方法
    public static void markSupportedCase() throws IOException{
        InputStream input=new FileInputStream(filePath);

        System.out.print(input.markSupported());
        input.mark(10);
        try {
            input.reset();
        }catch (IOException e){
            System.out.print(e.getMessage());
        }finally {
            input.close();
        }
    }
    //测试获取属性等方法
    public static void getPropertiesCase() throws IOException{
        FileInputStream input=new FileInputStream(filePath);
        System.out.println(input.getChannel());                    //获取文件FileChannel对象
        System.out.print(input.getFD());                           //获取文件FileDescriptor对象
    }
    
    public static void main(String[] args) throws IOException {
        readCase();
        System.out.println("\n-------------------------------");
        readByteArrayCase();
        System.out.println("\n-------------------------------");
        skipCase();
        System.out.println("\n-------------------------------");
        availableCase();
        System.out.println("\n-------------------------------");
        markSupportedCase();
        System.out.println("\n-------------------------------");
        getPropertiesCase();
    }
}
```
　　下面是控制台输出结果


>The quick brown fox jumps over a lazy dog.       
>\-------------------------------  
>The quick brown fox jumps over a lazy dog.          
>\-------------------------------  
>quick brown fox jumps over a lazy dog.                  
>\-------------------------------    
>42　　　　　　　　　　　　　　　　　　　　　　　　　            
>\-------------------------------   
>falsemark/reset not supported                            
>\-------------------------------  
>sun.nio.ch.FileChannelImpl@1540e19d                      
>java.io.FileDescriptor@677327b6   

由输出结果可以看出FileInputStream并没有重写markSupported()方法,所以FileInputStream并不支持mark()和reset()方法,读取文件时无法标记读取位置和重置.

## FileReader

　　FileReader直接继承自InputStreamReader,而InputStreamReader是一个输入转换流,将输入字节转换为字符形式,这说明FileReader底层还是依靠字节流读取,只不过java帮我们进行了编码工作.

# 文件写入

　　FileOutputStream和FileWriter是文件字节输出流和文件字符输出流,都是提供文件写入功能,只是写入形式不同,一个以字节为最小单位写入,一个以字符为最小单位写入,此外还可以选择是在原来文件基础上重新写入还是追加写入,写入结束后需要调用close()方法释放流对象.
  
>FileOutputStream没有缓冲区，其flush方法没有意义，而FileWriter有缓冲区，需要立即写入的话需要调用flush方法。

两个文件输出流的有参构造方法一致,以FileOutputStream为例

- public FileOutputStream(String name)
- public FileOutputStream(String name, boolean append)
- public FileOutputStream(File file)
- public FileOutputStream(File file, boolean append)
- public FileOutputStream(FileDescriptor fdObj)

其中有两个构造方法带有一个boolean类型形式参数,它规定写入形式,当为true时,表明要在原来文件尾追加写入,为false时是重写文件,不带此参数默认为false.

## FileOutputStream

　　FileOutputStream直接继承自OutputStream

```java
package com.java.io;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;

/**
 * Created by Administrator on 2017/3/20 0020.
 */
public class FileOutputStreamTest {
    public static final String filePath="C:\\Users\\Administrator\\Desktop\\outStream.txt";
    public static final String lineSeparator = System.getProperty("line.separator", "/n");  //获取当前系统的文本分隔符

    public static void main(String[] args) throws IOException {
        writeCase();
        constructorCase();
    }

    //写入方法方法测试,分别是write(int a),write(byte[] b),write(byte[] b,int off,int len)三种写入方式
    public static void writeCase() throws IOException {
        OutputStream output=new FileOutputStream(filePath);         //如果文件不存在,则创建该文件

        output.write(98);
        output.write(lineSeparator.getBytes());
        output.write('a');
        output.write(lineSeparator.getBytes());
        output.write("hello world".getBytes());
        output.write(lineSeparator.getBytes());
        output.write("hello world".getBytes(),2,3);
        output.close();
    }
    //构造方法测试,主要看追加写入效果演示
    public static void constructorCase() throws IOException{
        OutputStream output1=new FileOutputStream(filePath);
        output1.write('a');
        output1.close();

        OutputStream output2=new FileOutputStream(filePath,true);
        output2.write('b');
        output2.close();
    }
}
```
上面代码中当执行writeCase()方法后,桌面创建了outStream.txt文件夹,内容为
>b  
>a  
>hello world  
>llo  

需要注意的是FileOutputStream执行write()方法时,文件会立即写入内容,不需要调用flush()方法刷入,FileOutputStream也没有重写flush()方法,其方法体为空
执行constructorCase()方法第一次创建output1并写入时,文件outStream.txt被重新创建,文本内容为
>a  

第二次创建output2,写入模式变为追加写入,文件内容变为  
>ab

## FileWriter

　　FileWriter直接继承自OutputStreamWriter,而OutputStreamWriter是一个输出转换流,将输出的字符转换为字节形式,这说明FileWriter底层还是依靠字节流写入,只不过java帮我们进行了编码工作.
　　此外需要注意的是FileWriter具有一个缓冲区,写入的字符首先会存在缓冲区内,因此write()方法并不会直接写入到文本,

```java
package com.java.io;

import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;

/**
 * Created by Administrator on 2017/3/20 0020.
 */
public class FileWriterTest {
    public static final String filePath="C:\\Users\\Administrator\\Desktop\\writer.txt";
    public static final String lineSeparator = System.getProperty("line.separator", "/n");  //获取当前系统的文本分隔符

    public static void main(String[] args) throws IOException {
        writeAndFlushCase();
    }
    //测试 write()方法各种形式,一时flush()方法作用
    public static void writeAndFlushCase() throws IOException{
        Writer writer=new FileWriter(filePath);

        writer.write(98);
        writer.write(lineSeparator);
        writer.write("你好,世界".toCharArray());
        writer.write(lineSeparator);
        writer.write("你好,世界".toCharArray(),3,2);
        writer.flush();                                       //调用flush(),将缓冲区内字符刷入到文本
        writer.write(lineSeparator);
        writer.write("你好,世界");
        writer.write(lineSeparator);
        writer.write("你好,世界",1,4);

        writer.close();

    }
}
```
　　为了测试FileWriter的缓冲区效果,我们在debug模式下一行行执行,实例化FileWriter对象后,java会自动帮我们在所写路径下创建相应文件,如果原来不存在的话

在代码执行到flush()方法调用前,打开文本内容一直为空,调用flush()方法后,文本内容为
>b
>你好,世界
>世界

执行完close()前文本内容一直不变,调用close()后,再次查看文本内容为
>b
>你好,世界
>世界
>你好,世界
>好,世界

　　可见FileWriter使用写入方法时,会首先将字符写入到一个字符缓冲区,等缓冲区满后才会真正写入到文本,这样设计的目的我想不是为了提高写入速度,因为一是相对应的FileOutputStream类没有使用缓冲区,二是后面有专门的缓冲流,我们知道FileWriter继承自OutputStreamWriter,这是一个转换流,说明FileWriter本质上还是以字节的形式写入.只不过java自动帮我们编码,而之所以要使用一个缓冲区,就是为了每执行一次写入,确保写入的是一个字符.

　　此外FileWriter方法最后调用close()方法时并没有执行flush()方法,后面的同样被写入,说明FileWriter的close()方法会自动执行一次flush()操作,通过源码也会发现这一点.

# 随机文件读写

　　上面无论文件的读取只能从文件头开始,写入可以重写或者追加写入,而通过文件随机读写流RandomAccessFile,我们可以在任意指定位置进行读写

　　RandomAccessFile操作文件就像操作一个byte数组一样,可以通过一个文件指针(就像数组的索引),来实现从文件随机位置读写,

　　此外RandomAccessFile和上面文件流不同的是,它不是java IO四个基本流的直接或间接子类,而是实现了DataOutput,DataIntput,Closeable三个接口,它有两个构造方法

- public RandomAccessFile(String name ,String mode)
- public RandomAccessFile(File file ,String mode)

需要注意两个构造方法的第二个参数,String mode,它表示对文件操作的类型,分以下四种形式

|参数|意义|
|----|---|
|r|只读模式，如果对文件进行写操作，会报IOException错误|
|rw|读写模式,如果文件不存在,将会创建文件|
|rws|同步读写模式,任何对文件的修改(内容和元数据),都会立即被同步到底层存储设备ddd|
|rwｄ|同步读写模式,任何对文件的修改(内容),都会立即被同步到底层存储设备|

```java
package com.java.io;

import java.io.IOException;
import java.io.RandomAccessFile;

/**
 * 在桌面创建一个file.txt文本,内容为"The quick brown fox jumps over a lazy dog.",文件大小是42字节,
 * 主要测试以下方法
 *    read()              读取字节
 *    write(byte[] byte)  写入字节数组
 *    seek(long pos)      设置当前文件指针
 *    getFilePointer()    获取当前文件指针
 */
public class RandomAccesseFileTest {
    public static final String filePath = "C:\\Users\\Administrator\\Desktop\\file.txt";
    public static final String lineSeparator = System.getProperty("line.separator", "/n");  //获取当前系统的文本分隔符

    public static void main(String[] args) throws IOException {
        readCase();
        writeCase();
    }

    public static void readCase() throws IOException {
        RandomAccessFile file = new RandomAccessFile(filePath, "r");

        int data = file.read();
        while (data != -1) {
            System.out.print((char) data);
            data = file.read();
        }

        System.out.println(lineSeparator + "----------------------------");
        file.seek(4);
        System.out.println("文件现在指针位置  " + file.getFilePointer());
        data = file.read();
        while (data != -1) {
            System.out.print((char) data);
            data = file.read();
        }

        try {
            file.write(10);
        } catch (IOException e) {
            System.out.println(lineSeparator + "----------------------------");
            System.out.println(e);
        }finally {
            file.close();
        }
    }

    public static void writeCase() throws IOException {
        RandomAccessFile file = new RandomAccessFile(filePath, "rw");

        file.write("The tardy".getBytes());
        file.seek(4);
        file.write("quick".getBytes());
        file.close();
    }
}
```
执行完readCase()后控制台打出
>The quick brown fox jumps over a lazy dog.  
>\----------------------------  
>文件现在指针位置  4  
>quick brown fox jumps over a lazy dog.  
>\----------------------------  
>java.io.IOException: 拒绝访问。  

在debug模式下执行writeCase()第一次写入时,打开file.txt,文件内容是
>The tardy brown fox jumps over a lazy dog. 

重新设置文件指针为4,第二次写入后,查看文件,内容是  
>The quick brown fox jumps over a lazy dog.