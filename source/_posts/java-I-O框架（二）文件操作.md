title: java I/O框架（二）文件操作
id: 1493867141953
author: 不识
tags:
  - java
  - IO
categories: []
date: 2017-05-04 11:05:00
---
　
　　java io中最常操作的就是我们电脑中的文件,将这些文件以流的形式本地读写,或者上传到网络上.java中的File类就是对这些存储于磁盘上文件的虚拟映射,这也体现了java面向对象的思想,在学习io流对文件的读写前,我们要先学习下如何通过File何操作文件;
  
  <!-- more -->

# 构造方法

　　File类直接继承自Object,并且实现了Serializable和Comparable两个接口,实现Sericalizable接口表示File对象可以序列化,对象的序列化在最后我们还会提到,而实现Comparable接口,表示File对象可以用来比较排序,File的源码中重写了Comparable接口的compareTo()方法,通过获取底层操作系统,来对File路径名按字母排序;

　　首先我们看如何实例化一个File对象
```java
File file=new File("C:\\Users\\Administrator\\Desktop\\a.txt");
```
　　File提供了几个构造方法,大部分大同小异,上面是我们常用的一种,通过传入文件的路径名,来实例化File对象,不过需要注意的两点是

　　　　1. 这个路径名并不需要在磁盘中真正存在,构造方法也不会检查这一点

　　　　2. 文件路径名写法取决于你的操作系统,上面例子是在Windows系统中使用两个反斜线(\\)转义字符来表示一个反斜线(\),而在Unix系统中则是这样的"/home/myfile/data/a.txt",不过经测试,单斜线这种分隔符在Windows中同样可以,大家可以自己试一下
    

其他的构造方法,这里我也列出一下,比较简单,不需多说
```java
public File(String parent, String child)
public File(File parent, String child)
public File(URI uri)
``` 
# 成员方法

## 创建功能

- public boolean **createNewFile**()     创建新文件
- public boolean **mkdir**()                创建单级目录
- public boolean **mkdirs**()               创建多级目录

```java
 public static void fileTest() throws IOException {

        File dir = new File("C:\\Users\\Administrator\\Desktop\\a\\b");
        System.out.println(dir.mkdir());           //false   mkdir()只能创建单级目录

        System.out.println(dir.mkdirs());          //true    mkdirs()用于创建多级目录
        System.out.println(dir.mkdirs());          //false    已经存在的目录再次创建,则返回false

        File file=new File("C:\\Users\\Administrator\\Desktop\\a\\b\\test.txt");
        System.out.println(file.createNewFile());   //true    createNewFile()用于创建新文件,其上级路径必须存在,否则创建失败
        System.out.println(file.createNewFile());   //false   文件已存在则创建失败
    }
```
## 删除功能

- public boolean delete()        　　　　删除文件

## 判断功能

- public boolean **isDirectory**()    判断是否为文件夹
- public boolean **isFile**()       判断是否为文件
- public boolean **exists**()       判断路径是否存在
- public boolean **canRead**()      判断文件是否可读
- public boolean **canWrite**()      判断文件是否可写
- public boolean **isHidden**()      判断是否是隐藏文件

## 重命名功能

- public boolean ***renameTo**(File dest)  重命名原文件

```java
   public static void fileDeleteTest() throws IOException {
        File file=new File("C:\\Users\\Administrator\\Desktop\\a\\b\\test.txt");

        boolean a= file.renameTo(new File("C:\\Users\\Administrator\\Desktop\\renameTest.txt"));   
　　　　 //重命名文件的同时,还可以移动文件到新的位置
　　　　 System.out.print(a);                                                                       
　　　　 //true  返回值表示重命名成功
    }
```

## 基本获取功能

- public String **getAbsolutePath**()  获取绝对路径
- public String **getPath**()       获取全部路径
- public String **getName**()       获取文件或目录名
- public long **length**()         获取文件大小
- public long **lastModified**()     获取上次修改的时间

```java
 public static void fileGetMethod() throws IOException {

        File file=new File("C:\\Users\\Administrator\\Desktop\\a\\b\\test.txt");

        System.out.println(file.getAbsolutePath());　　　　　　 //  "C:\\Users\\Administrator\\Desktop\\a\\b\\test.txt"
        System.out.println(file.getName());　　　　　　　　　 　 //   test.txt
        System.out.println(file.getPath());　　　　　　　　　 　 //  "C:\\Users\\Administrator\\Desktop\\a\\b\\test.txt"
        System.out.println(file.length());　　　　　　　　　　 　//   0
        System.out.println(new Date(file.lastModified()));　　//   Wed Mar 15 13:58:50 GMT+08:00 2017

    }
```
 

## 高级获取功能

- public String[] **list**()  获取该目录下所有文件和文件夹，返回他们文件名字符串数组
- public File[] **listFiles**()  获取该目录下所有文件和文件夹，返回他们的文件File对象数组