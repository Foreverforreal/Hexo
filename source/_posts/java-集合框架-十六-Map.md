title: java集合框架（十六）Map
id: 1493365316494
author: 不识
tags:
  - java
  - 集合
categories:
  - java集合框架
date: 2017-04-28 15:41:00
---
# 概述
![Map](http://www.processon.com/chart_image/58eca417e4b0c9097c3897b2.png)

　　Map是一个包含键值对的集合,一个map不能有重复的键(key),而且每个键至多只能对应一个值.Map同Collection一样,它的所有通用实现都会提供一个转换器构造函数,接收一个Map类型集合,并以此初始化自己,这样只要是Map的实现都可以相互之间转换.  
　　和List与Set一样,Map强化了equal和hashCode以能对两个Map对象实现逻辑上的比较.如果两个Map实例有相同的键值对,那么它们是相等的.  
  
>Map的集合视角方法使Map可以像Collection一样进行操作元素
  
- KeySet--返回Map集合中键的Set集合
- Values--返回Map集合中值的Collection集合
- entrySet--返回Map集合中键值对对象的Set集合.在Map中提供了一个小的嵌套接口Map.Entry,它就是Map的键值对对象.

<!-- more -->

Map没有实现Iterable接口,所以集合视角是Map集合遍历的唯一手段,并且每次获取Map集合视角的时候,返回的是相同的对象.集合视角支持removal类型操作,但是任何情况下都不支持addition,对集合视角的removal操作会影响到Map集合本身,比如map.keySet().clear()将会清空map,反之亦然,这和Set,List的视图一样.

使用集合视角有一些有意思的用法,比如判断一个Map是否是另一个Map的子集
```java
if(m1.entrySet().containsAll(m2.entrySet())) {}
```
类似,也可以判断两个Map的是否拥有相同的键
``` java
if(m1.keySet().equals(m2.keySet())) {}
```

# SortedMap与NavigableMap

　Map和Set接口从形式上有些类似,类比与SortedSet和NavigableSet,Map也有SortedMap和NavigableMap两个接口,实际上Set的实现底层就是使用的Map存储数据.

　　SortedMap将元素的键以自然排序,或者依照给定的排序器来进行排序,同SortedSet,SortedMap提供了以下几种操作.

- **视图**--允许从SortedMap截取并返回任意范围的元素视图
- **端点操作**---可以直接获取集合头或尾的元素
- **排序器**---返回用于排列元素的排序器   
　　Map集合并无法直接实现遍历,而是通过它的集合视角遍历元素,所以SortedMap在返回的集合视角中,集合视角的迭代器也将会按SortedMap的顺序进行排序,同理的SortedMap中toArray方法返回的数组也是如此,toString方法会返回一个包含所有元素,并排序好的字符串.
  
# 实现
　　Map的实现可以分成**通用实现**,**专用实现**,并发实现

## 　通用实现
　　通用实现有三个,**HashMap**,**TreeMap**和**LinkedHashMap**.如果我们想要对元素进行一些排序操作,那么应当使用TreeMap,如果我们想要最好的性能而不在乎是否排序,应当使用HashMap,如果需要和HashMap接近的性能,并且可以以插入顺序遍历,那么应当使用LinkedHashMap.这和Set的通用实现很类似.

　　此外LinkedHashMap不仅提供了插入排序(insert order),同时还提供访问排序(access order),这样LinkedHashMap非常适用做本地缓存类(LRU)
## 　专用实现
　　专用实现也有三个,分别是**Enummap**,**WeakHashMap**和**IdentityHashMap**。

- EnumMap是一个高性能的以枚举为键的Map集合,它内部是以数组实现.EnumMap将Map集合的丰富功能和安全性与数组的快速访问结合起来,如果想要实现一个用枚举映射值得结构,应当使用EnumMap.

- WeakHashMap只存储弱引用类型的key,当它内部的元素的键不再被外界引用时,其键值对就可以被垃圾回收期(GC)回收,被从WeakHashMap中移除.WeakHashMap提供最简单利用弱引用的方法,这对实现”registry-like”数据结构非常有用.

- IdentityHashMap存储元素时,不使用equal方法比较键对象,而是使用==来对比,适用于实现对象拓扑结构转换,比如对象序列化或深度拷贝时,作为一个”节点表”来跟踪处理那些已经处理过的对象引用.   

Java.util.concurrent包含ConcurrentMap接口,它继承自Map,其putIfAbsent,remove,和replace方法是原子性的.ConcurrentHashMap是它的实现.

ConcurrentHashMap是一个高并发高性能的基于哈希表的实现,当检索元素时永不会阻塞,并且当执行update允许客户端选定执行并发级别更新.它是HashMap的替代,ConcurrentHashMap除了实现ConcurrentMap还支持HashTable所有遗留的独有的操作.