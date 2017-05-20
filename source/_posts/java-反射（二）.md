title: java 反射（二）
id: 1494489233857
author: 不识
tags:
  - java
  - 反射
categories:
  - java 基础
  - java 反射
date: 2017-05-11 15:53:00
---
在Java反射的包中定义一个接口java.lang.reflect.Member，它有三个实现类java.lang.reflect.Field, java.lang.reflect.Method, and java.lang.reflect.Constructor.同时这三个类的成员对象直接或间接还都继承了AccessibleObject,当类成员是私有的时候，如果我们使用反射来对这些类成员进行修改或调用，需要使用AccessibleObject的setAccessible()方法来绕过默认的Java访问权限检测，来实现对私有成员的使用。
>根据Java语言规范，类成员是可继承的类组件，包括字段，方法，以及嵌套的类，接口，枚举类型。因为构造器是不可继承的，所以它不是类成员，这与java.lang.reflect.Member.的实现类是有些差别的。

在Class中提供获取类成员的方式有两种，一种获取不同数量类成员，一种根据类成员性质，根据类成员数量的是要么我们获取所有成员列表，要么根据给定参数获取指定的类成员。而根据类成员性质的，则是一种获取类所有公共成员，包括从父类继承而来的，一种则是获取类内声明所有成员，不包含从父类继承，但是包含私有成员。

<!-- more -->

***
# 字段
***
## 获取Field
|Class API|列举成员|继承成员|私有成员|
|---------|--------|--------|--------|
|getDeclaredField()|X|X|O|
|getField()   |X|O|X|
|getDeclaredFields()|O|X|O|
|getFields()|O|O|X|

我们既可以列举类中所有字段，也可以根据已知的名称来获取一个特定的字段，如果该名称字段不存在的话，会抛出NoSuchFieldException异常。

```java
public class FieldSpy {
    public String s;
    public boolean[][] b;
    public ArrayList arrayList;
    private int i;
    private char c;

    public static void main(String[] args) throws NoSuchFieldException {
        Class fieldSpy = FieldSpy.class;

        System.out.println("---------公共字段列表---------------");
        Field[] fields = fieldSpy.getFields();
        for (Field field : fields) {
            System.out.println(field.toGenericString());
        }

        System.out.println("---------私有字段i---------------");
        Field field3 = fieldSpy.getDeclaredField("i");
        System.out.println(field3.toGenericString());
    }
}
```
控制台输出  

>---------公共字段列表---------------    
>public java.lang.String com.java.reflection.FieldSpy.s  
>public boolean\[][] com.java.reflection.FieldSpy.b  
>public java.util.ArrayList com.java.reflection.FieldSpy.arrayList  
>---------私有字段i---------------  
>private int com.java.reflection.FieldSpy.i  

## 获取设置Field值

获取Filed对象后，我们可以使用它的get(Object obj)和set(Object obj,Object value)来获取和设置一个申明该字段的对象的字段值，需要注意的是如果该字段为类私有，我们要先通过setAccessible()方法来设置访问权限

```java
 i.setAccessible(true);
        FieldSpy fieldSpyInstance = new FieldSpy();

        System.out.println(i.get(fieldSpyInstance));
        i.set(fieldSpyInstance,25);
        System.out.println(i.get(fieldSpyInstance));
```
控制台输出

>0
>25

***
# 方法
***
## 获取Constructor

|Class API|列举成员|继承成员|私有成员|
|---------|--------|--------|--------|
|getDeclaredMethod()|X|X|O|
|getMethod()|X|O|X|
|getDeclaredMethods()|O|X|O|
|getMethods()|O|O|X|

我们既可以获取类的成员方法列表，也可以根据已知的方法名和参数来获取指定的方法Method对象。当获取指定的方法时，如getMethod(String name, Class<?>... parameterTypes)我们需要传入两种参数，第一种是方法名，第二种该方法需要的参数，如果该方法的参数是泛型类型的，我们需要传入的是Object.class。

```java
   Class c = Reader.class;

        System.out.println("---------方法声明列表---------------");
        Method[] methods = c.getDeclaredMethods();
        for (Method method : methods) {
            System.out.println(method.toGenericString());
        }

        System.out.println("---------公共方法read---------------");
        Method method = c.getMethod("read");
        System.out.println(method.toGenericString());

```
控制台输出

>---------方法声明列表---------------  
>public boolean java.io.Reader.ready() throws java.io.IOException  
>public int java.io.Reader.read() throws java.io.IOException  
>public abstract int java.io.Reader.read(char[],int,int) throws java.io.IOException 
>public int java.io.Reader.read(char[]) throws java.io.IOException  
>public int java.io.Reader.read(java.nio.CharBuffer) throws java.io.IOException   
>public abstract void java.io.Reader.close() throws java.io.IOException   
>public void java.io.Reader.mark(int) throws java.io.IOException   
>public boolean java.io.Reader.markSupported()   
>public void java.io.Reader.reset() throws java.io.IOException    
>public long java.io.Reader.skip(long) throws java.io.IOException   
>---------公共方法read---------------   
>public int java.io.Reader.read() throws java.io.IOException  



## 调用Method
获取Method对象后，使用它的invoke(Object obj,Object... args)来调用该方法，invoke需要传入一个声明该方法的对象，否则则会抛出IllegalArgumentException异常，后面是一个可变参数，传入该方法所需的实际参数。

```java
        Method toUpperCase = String.class.getMethod("toUpperCase");
        System.out.println(toUpperCase.invoke("hello"));
```

控制台输出
>HELLO

这里需要注意的是，当方法是私有方法时，同样需要先设置Method对象访问权限。


***
# 构造器
***

## 获取Constructor

|Class API|列举成员|私有成员|
|---------|--------|--------|--------|
|getDeclaredConstructor()|X|O|
|getConstructor()|X|X|
|getDeclaredConstructors()|O|O|
|getConstructors()|O|X|
>构造器是无法继承的

以获取公开构造器为例，我们既可以列举类中所有构造器，也可以根据构造器的参数来获取特定的一个构造器。

```java

    	Class c = ArrayList.class;

        Constructor<?>[] constructors = c.getConstructors();
        System.out.println("---------构造方法列表---------------");
        for (Constructor<?> constructor : constructors) {
            System.out.println(constructor.toGenericString());
        }
        
        System.out.println("----------有参构造（int）--------------");
        Constructor constructor = c.getConstructor(int.class);
        System.out.printf(constructor.toGenericString());
```
      
控制台输出
>---------构造方法列表---------------  
>public java.util.ArrayList(java.util.Collection<? extends E>)  
>public java.util.ArrayList()  
>public java.util.ArrayList(int)  
>----------有参构造（int）--------------  
>public java.util.ArrayList(int)  



## 利用Constructor初始化对象

我可以通过Constructor对象的newInstance(Object... initargs)来新建一个对象，它接收一个可变参数.需要注意的是Constructor是采用泛型机制的，如果我们没有指定Constructor的泛型参数，初始化一个对象的时候需要进行强制类型转换。

```java
       Class c = ArrayList.class;

        Constructor<ArrayList> constructor = c.getConstructor(int.class);
        ArrayList list =  constructor.newInstance(10);
```