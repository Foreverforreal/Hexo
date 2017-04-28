title: java 集合框架(一)概述
categories:
  - java集合框架
tags:
  - java
  - 集合
author: 不识
date: 2017-04-24 17:49:00
---
## 概述
***
　　Java Collection Framework (JCF) 提供给我们一系列的类和接口,方便开发者处理集合对象.
　　在Java2之前，Java是没有完整的集合框架的。它只有一些简单的可以自扩展的容器类，比如**Vector，Stack，Hashtable**等。这些容器类在使用的过程中由于效率问题饱受诟病，因此在Java 2中，Java设计者们进行了大刀阔斧的整改，重新设计，于是就有了现在的集合框架。需要注意的是，之前的那些容器类库并没有被弃用而是进行了保留，主要是为了向下兼容的目的，但我们在平时使用中还是应该尽量少用。
　　Java集合框架大部分在java.util包中,此外还有一系列的并发集合在java.util.concurrent包中.在Java 7之后添加了泛型机制,使集合框架中在运行时期可能出现的类型转换问题,提前到编译时期来检查.
<!-- more -->
## 框架结构
***
![JCF结构图][1]
　  从上图可以看出,java集合框架主要分两大类型,一种是**集合(Collection)**,另一种是**图(Map)**,Collection我们可以理解为一个大小可变,提供各种操作数据方法的数组,而Map是一种key-value数据结构的集合.Java集合框架的通用实现类的都提供两个"标准"的构造方法,一个无参构造,一个用来初始化集合大小的有参构造,但这并不是java的强制规范,但是集合框架中所有的通用类都遵循这个规则.
### *Collection*
　　Collection是存储一组对象的集合容器,它主要有以下三个接口子类:

| 接口名 | 描述| 
|-----|------|
| List | 重复有序集合 | 
| Set  | 无序元素不重复集合 | 
| Queue |  JDK 1.5后新添加的队列数据结构集合,主要是为了存储数据而设计的,而不是处理数据|  

### *Map*
Map是一种键值对结构的集合，它保存的键是唯一的，并且一个键只能对应一个值
> Map并不是一个真正意义上的集合（are not true collections），但是这个接口提供了三种“集合视角”（collection views ），使得可以像操作集合一样操作它们

*   把map的内容看作key的集合(Set<K> keySet())
*   把map的内容看作value的集合(Collection<V> values)
*   把map的内容看作key-value映射的集合(Set<Map.Entry<K,V>> entrySet())

## 通用实现
***
集合框架具体实现子类,可由不同接口类型集合与不同数据结构组合而成

|接口 |哈希表|可变数组|平衡二叉树|链表|链表+哈希表|
|-----|------|--------|----------|----|----|
|**List** |    |*ArrayList*|   |*LinkedList*||
|**Set**  |*HashSet*|     |*TreeSet* | |*LinkedHashSet*|
|**Deque**|   |*ArrayDeuque*|   |*LinkedList*||
|**Map**  |*HashMap*|  |*TreeMap*|*LinkedHashMap*|*LinkedHashMap*|

　　通用接口实现类支持接口中所有可选的数据操作,并且没有任何的限制.这些通用类都是非同步的,如果要在多线程环境下使用线程安全的同步集合,可以调用Collections中的静态同步封装器,它可以将非同步的集合封装成同步集合.此外这些实现都有fail-fast机制的迭代器,可以用来探测无效的并发修改,快速而简洁的抛出错误.详细的可见[Java ConcurrentModificationException][2]  
　　此外,为了保证集合框架的核心接口易于管理,一些修改集合的操作方法在接口中是被设计为可选的,一些通用实现可以选择不支持此类操作方法,如果这些方法被调用的话,则会抛出UnsupportedOperationException,例如在AbstractList类中,调用有些方法,比如add(int,E)就会直接抛出UnsupportedOperationException异常,子类必须重写这个方法才能支持添加元素操作,否则就意味着这个子类是被设计为不可变集合.但是在java集合框架中所有的通用实现类都支持所有的元素操作方法.

## 目录
* - [一. Iterable接口]()
  - [二. Collection集合]()
     - [1.Set接口]()
         - [1.1 HashSet]()
         - [1.2 TreeSet]()
         - [1.3 LinkedHashSet]()
     - [2.List接口]()
     - [3.Queue接口]()
     - [4.Deque接口]()
 - [三. Map集合]()
 - [四. Collections与包装实现，便利实现]()
 - [五. 便利实现]()
 - [六. 聚合操作]()
 - [七. 并发集合]()

## 参考 
***
 >[Java集合框架综述](http://liujiacai.net/blog/2015/09/01/java-collection-overview/)  
 >[Java Collection Framework Tutorials](https://docs.oracle.com/javase/8/docs/technotes/guides/collections/index.html)  
 >[JAVA基础拾掇-集合篇（一）](http://www.myexception.cn/java-other/2033553.html)  
 >[关于 Java Collections API 您不知道的 5 件事](https://www.ibm.com/developerworks/cn/java/j-5things2.html)
 
  [1]: http://www.processon.com/chart_image/58dcc48be4b0a49a5fae4955.png
  [2]: http://www.2cto.com/kf/201403/286536.html