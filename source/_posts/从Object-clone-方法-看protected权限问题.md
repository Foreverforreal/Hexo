title: '从Object.clone()方法,看protected权限问题'
id: 1493433253761
author: 不识
tags:
  - java
categories:
  - java遇到的问题
date: 2017-04-29 10:34:00
---
学习java权限修饰符时,权限那张表里可以看到,被protected修饰的类成员,是可以在同包类,或者不同包的子类中访问的,然而当使用类的clone()方法时,却出现了方法不可见的编译错误

```java
Class Person implements Cloneable{
}

Class Test{
     public void main(String [] args) throws ClontNotSupportedException{
          Person person =new Person();
          person.clone();          //Compile error (The method clone() from the type Object is not visible)
    }
}
```
<!-- more -->
上面代码中Test与Person在同一个包中,为何调用person.clone()会报错呢?再看下面的代码

```java
Class Person implements Cloneable{
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
 }
 
 Class Test{
      public void main(String [] args) throws ClontNotSupportedException{
           Person person =new Person();
           person.clone();          //Compile succes
     }
 }
```

我们知道,clone()方法是继承自父类Object,其方法体是   protected native Object clone() throws CloneNotSupportedException;

通过多次测试,我发现

>"当父类与子类不在同一个包时(Object与Person)，创建子类对象实例(person)的类(Test)如果与父类(Object)不在同一个包,受保护的成员(clone方法)是无法访问的,如果我们在父类(Object)的包中创建测试类,同样创建子类实例(Person),这时受保护成员则可以访问"

>"在上面第二个代码中,我们在子类Person中重写了受保护成员clone(),在测试类Test中编译通过,可以访问"

通过上面的对比,我觉得可以这样理解,

1. 子类没有重写受保护方法,就看作是使用的父类的方法,这样测试类Test与父类Object是不同包的关系,所以无法调用

2. 如果我们把测试类Test挪到Object所在的包,可以调用

3. 如果子类Person重写了clone方法,调用的就是子类中clone方法,子类Person与测试类Test在同一个包中,所以可以调用