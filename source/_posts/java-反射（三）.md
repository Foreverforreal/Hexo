title: java 反射（三）
id: 1494582516337
author: 不识
tags:
  - java
  - 反射
categories: []
date: 2017-05-12 17:48:00
---


一个动态代理类是一个在在运行时期被指定实现一系列接口的类，使得一个类实例上的接口方法被调用时，将会通过一个统一的接口来编码转发给另一个对象。因此一个动态代理类可以用来为一系列接口创建一个类型安全的代理对象，而不用需要预先生成代理字节码文件。一个动态代理类实例的方法调用会被转发给该类实例 invocation handler中的一个单独的方法，该方法也会被编码成java.lang.reflect.Method对象，来标识此方法被调用。


动态代理有很多用途，比如数据库连接，事务管理，单元测试的动态对象模拟，和其他的AOP类似的方法拦截等用途。

<!-- more -->

# 创建代理

Java动态代理机制中，我们有两个重要的接口或类，一个是**InvocationHandle**，一个是**Proxy**，他们都在java.lang.reflect包中。

## Proxy
Proxy是代理中最重要的一个类，它包含了一系列和动态代理相关的方法，Proxy用来创建代理对象实例的方法是newProxyInstance
```java
 public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces,
InvocationHandler h)throws IllegalArgumentException
```

它有三个参数
- ClassLoader loader  指定了加载代理对象的类加载器
- Class<?>[] interfaces 表示代理对象要实现的一系列接口
- InvocationHandler h  代理对象的所有方法调用都会被转发到InvocationHandle中

## InvocationHandle
每一个动态代理类都必须要实现InvocationHandler这个接口，并且每个代理类的实例都关联到了一个handler，当我们通过代理对象调用一个方法的时候，这个方法的调用就会被转发为由InvocationHandler这个接口的 invoke 方法来进行调用。InvocationHandler中只有要给方法Invoke
```java
public Object invoke(Object proxy, Method method, Object[] args)throws Throwable;
```

方法有三个参数

- Object proxy 指动态生成的代理对象,由Proxy.newProxyInstance()方法传入，不需要我们指定
- Method 指我们要调用的真实对象方法的Mehthod对象
- Object[] 调用方法的参数，**这里原始类型数据(int ,boolean...)会被包装为其对应的包装类(Integer,Boolean...)**


## 代理实例

先定义一个接口UserIn
```java
public interface UserIn {
    void say();
}
```
然会定义一个实现了该接口的类User
```java
public class User implements UserIn {
    @Override
    public void say() {
        System.out.println("User say hello");
    }
}
```
接着就是定义一个动态代理类，该类实现了InvocationHandle接口，定义了我们要实际执行的代理操作。

```java
public class DynamicProxy implements InvocationHandler {
    private Object obj;

    public DynamicProxy(Object obj){
        this.obj = obj;   //构造方法传入我们要代理的真实对象
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        System.out.println("*****before User say*****"); //这里在我们调用真实对象方法前，附加一些操作
        method.invoke(obj,args);               //调用真实对象obj的方法
        System.out.println("*****after User say*****");  //这里在我们调用真实对象方法后，附加一些操作

        return null;  //因为要代理的只有一个方法，其没有返回值，这里直接返回null
    }
}
```
下面创建代理对象，并调用它的方法
```java
  //传入一个User对象作为真实对象给动态代理类
        DynamicProxy dynamicProxy = new DynamicProxy(new User());

        //使用动态代理类的类加载器来加载代理对象
        ClassLoader cl = dynamicProxy.getClass().getClassLoader();
        //使用真实对象实现的接口,这样代理对象就与真实对象“相同”类型，并且可以调用同样的方法
        Class[] classes = {UserIn.class};

        //这里就是使用以上参数直接生成代理对象，因为我们给定接口类型为UserIn,所以这里可以直接强制转换类型
        UserIn user = (UserIn) Proxy.newProxyInstance(cl, classes, dynamicProxy);
        
        //因为生成代理对象时，实现的是UserIn接口，所以可以调用其方法
        user.say();            

        System.out.println("--------------------------------");
        System.out.println("Proxy Object Type: " + user.getClass().getName());

```
执行程序后，控制台输出
>\*\*\*\*\*before User say\*\*\*\*\*   
>User say hello  
>\*\*\*\*\*after User say\*\*\*\*\*    
>\--------------------------------   
>Proxy Object Type: com.sun.proxy.$Proxy0

从输出可以看出，调用代理对象的方法后，其方法实际执行会转发到动态代理类DynamicProxy中的invoke方法中。此外我们输出的代理对象类型为com.sun.proxy.$Proxy0，而不是我们是使用的UserIn接口，这是因为代理对象是由JVM在运行时动态生成的，由JVM动态生成的代理对象名称都以$Proxy开头，后加上该代理对象的标号。

>[java的动态代理机制详解](http://www.cnblogs.com/xiaoluo501395377/p/3383130.html)