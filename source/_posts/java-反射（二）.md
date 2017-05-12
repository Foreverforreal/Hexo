title: java 反射（二）
id: 1494489233857
author: 不识
tags:
  - java
  - 反射
categories: []
date: 2017-05-11 15:53:00
---


在Java反射的包中定义一个接口java.lang.reflect.Member，它有三个实现类java.lang.reflect.Field, java.lang.reflect.Method, and java.lang.reflect.Constructor.同时这三个类的成员对象直接或间接还都继承了AccessibleObject,当类成员是私有的时候，如果我们使用反射来对这些类成员进行修改或调用，需要使用AccessibleObject的setAccessible()方法来绕过默认的Java访问权限检测，来实现对私有成员的使用。
>根据Java语言规范，类成员是可继承的类组件，包括字段，方法，以及嵌套的类，接口，枚举类型。因为构造器是不可继承的，所以它不是类成员，这与java.lang.reflect.Member.的实现类是有些差别的。

在Class中提供了两种类型方法获取这些类成员，一种列举所有成员，一种搜索特定的成员，同时还有一些方法来访问类上声明的成员，而不是继承来的成员。 

<!-- more -->
# 字段

|Class API|列举成员|继承成员|私有成员|
|---------|--------|--------|--------|
|getDeclaredField()|X|X|O|
|getField()   |X|O|X|
|getDeclaredFields()|O|X|O|
|getFields()|O|O|X|

- **方法**

|Class API|列举成员|继承成员|私有成员|
|---------|--------|--------|--------|
|getDeclaredMethod()|X|X|O|
|getMethod()|X|O|X|
|getDeclaredMethods()|O|X|O|
|getMethods()|O|O|X|

# 构造器

## 获取Constructor

|Class API|列举成员|私有成员|
|---------|--------|--------|--------|
|getDeclaredConstructor()|X|O|
|getConstructor()|X|X|
|getDeclaredConstructors()|O|O|
|getConstructors()|O|X|
>构造器是无法继承的

以获取公开构造器为例，我们既可以列举类中所有构造器，也可以根据构造器参数来获取特定的一个构造器。

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
