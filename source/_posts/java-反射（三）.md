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
无论是代理类或者它们的实例，都是使用java.lang.reflect.Proxy的静态方法来创建的。



# 创建代理类

```java
 public static Class<?> getProxyClass(ClassLoader loader,Class<?>... interfaces)throws IllegalArgumentException

```
Proxy.getProxyClass方法通过传入一个类加载器和一个接口数组返回一个代理的Class对象。这个代理类将会由指定的类加载器加载，并且实现所有我们提供的接口。如果在一个类加载内，已经存在了实现同样接口的代理类，那我们再创建时，则会直接返回该已经存在的代理类。需要注意的是，这需要给定的接口顺序一致，一个类加载器内，实现了相同的接口，但是顺序不同的话，产生的代理类并不不是同一个。

## 代理类属性

- 代理类都是以public final修饰，而不是abstract
- 代理类名称以“$Proxy”开头
- 代理类都是继承自java.lang.reflect.Proxy
- 代理类按顺序实现了所有接口
- 如果一个代理类实现了一个非公开接口，那么代理类会被定义在和该接口一个包中。否则的话代理类的包是未指定的。
- 因为一个代理类创建时实现了所有指定的接口，所以调用getInterfaces方法时会返回该接口数组，并且顺序和创建时给定的接口数组一致。
- Proxy.isProxyClass方法当传入一个代理类时，将会返回true,无论这个代理类是Proxy.getProxyClass创建还是Proxy.newProxyInstance创建的。


# 创建代理对象

每一个代理类都有一个公开构造器，该构造器只有一个参数，为InvocationHandle接口的实现类。
与其通过反射API来获取代理类的公开构造器去创建一个代理类实例，我们同样可以通过Proxy.newProxyInstance方法来新建一个代理实例。


## Proxy.newProxyInstance()

```java
 public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces,
InvocationHandler h)throws IllegalArgumentException
```

newProxyInstance方法会创建一个代理类，并通过传入的InvocationHandle来实例化该类，它有三个参数
- ClassLoader loader  指定了加载代理对象的类加载器
- Class<?>[] interfaces 表示代理对象要实现的一系列接口
- InvocationHandler h  代理对象的所有方法调用都会被转发到InvocationHandle中

## InvocationHandle
每一个动态代理类都必须要实现InvocationHandler这个接口，并且每个代理类的实例都关联到了一个ivocation  handler，当我们通过代理对象调用一个方法的时候，这个方法的调用就会被转发为由InvocationHandler这个接口的 invoke 方法来进行调用。
```java
public Object invoke(Object proxy, Method method, Object[] args)throws Throwable;
```

方法有三个参数

- Object proxy 指动态生成的代理对象,由Proxy.newProxyInstance()方法生成代理对象传入，不需要我们指定
- Method 指我们要调用的真实对象方法的Mehthod对象
- Object[] 调用方法的参数，**这里原始类型数据(int ,boolean...)会被包装为其对应的包装类(Integer,Boolean...)**

invoke方法返回值会称为代理对象调用真实方法的返回值，如果该真实方法在接口中定义为原始类型，那么这个返回值通过invoke调用后，必须是该原始类型包装类的实例对象。**如果接口中方法定义返回值为原始类型，而invoke返回null,则会抛出NullPointerException异常**。如果接口中定义方法返回指与invoke返回指类型不兼容，则会抛出ClassCastException 异常。

对于一个代理对象实例，如果我们想要获取和它关联的invocation handle,可以使用Proxy.getInvocationHandle(Object proxy)方法。该方法如果传入对象不是代理对象，则会抛出IllegalArgumentException异常。

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

# 序列化

java.lang.reflect.Proxy实现了序列化接口，所以所有的代理实例都可以被序列化。如果一个代理实例包含的invocation handler没有实现序列化接口的话，将该实例写入java.io.ObjectOutputStream时会抛出NotSerializableException异常

。。。。

>[java的动态代理机制详解](http://www.cnblogs.com/xiaoluo501395377/p/3383130.html)