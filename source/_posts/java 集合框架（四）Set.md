title: java 集合框架(四)Set
id: 1493362637300
author: 不识
categories:
  - java集合框架
tags:
  - java
  - 集合
date: 2017-04-28 14:57:28
---
# 概述
![Set](http://www.processon.com/chart_image/58e85dc4e4b0c32f7cf0e28f.png)
　　Set是一种没有重复元素的集合,它所有的方法都是直接继承自Collection接口,但是添加了一个对重复元素的限制.Set要求元素强化equals和hashCode两个方法,以使Set集合可以对元素进行排序和对比.
  Set中没有新添方法,而是在子接口SortedSet和NavigableSet中拓展了一些功能
<!-- more -->
# SortedSet

|修饰符和返回值|方法名|描述|
|--------------|------|----|
|**端点操作**|||
|E|**first**()|返回当前集合第一个元素(低位),没有时抛出异常|
|E|**last**()|返回当前集合最后一个元素(高位),没有时抛出异常|
|**视图功能**|||
|SortedSet&lt;E&gt;|**subSet**(E，E)|返回指定两元素间元素组成的集合|
|SortedSet&lt;E&gt;|**headSet**(E)|返回指定元素之前元素组成的集合,不包含指定元素|
|SortedSet&lt;E&gt;|**tailSet**(E)|返回指定元素之后元素组成的集合,包含指定元素|
|**判读功能**|||
|Comparator&lt;? extend E&gt;|**comparator**()|返回排序器|

SortedSet内的元素以自然排序方式维持升序排序,或者依照指定的排序器排序,SortedSet集合相比Set添加了以下对元素操作的方式

- **视图**--允许从SortedSet截取并返回任意范围的元素视图
- **端点操作**---可以直接获取集合头或尾的元素
- **排序器**---返回用于排列元素的排序器  

需要格外注意的是,SortedSet视图的端点指向的是存储元素的内存空间,而不是给定的端点元素,视图仅仅是一个查看原集合的窗口,因此任何对视图的操作都会影响原集合,反之亦然.SortedSet在选取视图的时候,需要给定视图的截取的端点,并且含头不含尾,如果想要一个闭区间,同时包含两端点,可以在尾端点后加”/0”(空白字符),这样按照自然排序,前面一个字符自然就是我们给定的尾端点元素.

# NavigableSet
|修饰符和返回值|方法名|描述|
|--------------|------|----|
|**导航功能**|||
|E|**lower**(E)|返回指定对象之前的元素,没有时返回null|
|E|**floor**(E)|返回小于或等于指定对象的元素,没有时返回null |
|E|**higher**(E)|返回指定对象之后的元素,没有时返回null|
|E|**ceiling**(E)|返回大于或等于指定对象的元素,没有时返回null|
|**视图功能**|||
|NavigableSet&lt;E&gt;|**subSet**(E,boolean,E,boolean)|返回指定端点间元素组成的集合,布尔值决定是否包含指定元素|
|NavigableSet&lt;E&gt;|**headSet**(E,boolean)|返回指定端点前元素组成的集合,布尔值决定是否包含指定元素|
|NavigableSet&lt;E&gt;|**tailSet**(E,boolean)|返回指定端点后元素组成的集合,布尔值决定是否包含指定元素|
|NavigableSet&lt;E&gt;|**decendingSet**()|返回与原集合相反排序的集合|
|**弹出功能**|||
|E|**pollFirst**()|移除并返回集合第一个元素,集合为空时返回null|
|E|**pollLast**()|移除并返回集合最后一个元素,集合为空时返回null|
|**迭代器**|||
|Iterator&lt;E&gt;|**decendingIterator**()|获取集合的降序迭代器|

　　NavigableSet接口继承自SortedSet,视图操作上相比SortedSet,NavigableSet不仅多了一个decendingSet()获取反相排序的集合,而且subSet,headSet,tailSet还多了一个boolean类型参数,这个参数决定返回集合视图中是否包含给定的元素.NavigableSet还有一系列的导航方法,可以更具给定对象在集合内向前或向后寻找满足条件的元素

# 实现
Set接口的实现分为**通用实现**和**专用实现**

## 通用实现

通用实现类主要有三个**HashSet**,**LinkedHashSet**和**TreeSet**.
- HashSet通过哈希表存储元素,它是Set通用类中性能最好的一个,但不保证元素的排序.
- TreeSet以红黑树结构存储数据,它的元素按一定规则排序,所以他的性能要比HashSet差许多.
- LinkedHashSet在HashSet的基础上,增添了一个链表结构,来保证数据的按插入先后存储有序,因为需要维持一个链表,所以它的性能比HashSet稍微差一点,介于HashSet和TreeSet之间.

HashSet的性能开销在集合内元素数和集合容量上都是线性的,因此HashSet初始化太大会浪费空间和时间,太小的话,在扩容的时候数据结构的拷贝浪费很多时间,如果不指定初始化大小,集合容量默认是16.过去指定一个初始化大小有一定好处,但现在不再是这样了.HashSet还有一个被称为负载系数的调优参数,但一般都是使用默认值,如果不设定负载系数的话,我们最好将初始化大小定义为两倍我们需要的值,即使用不到这么多,一般也不是什么大问题

## 专用实现

专用实现类主要有两个,**EnumSet**和**CopyOnWriteArraySet**.
- EnumSet是一个高性能的枚举类型的Set实现类,其内部元素必须都是相同的枚举类型.
- CopyOnWriteArraySet是一个支持COW(copy-on-write)机制的集合.CopyOnWriteArraySet对集合的任何修改操作如,add,remove,set时,都会先复制一份,所以在CopyOnWriteArraySet可以安全的并发进行迭代和元素插入删除操作,不需要同步锁,实现了读写分离,但是读操作不具备实时性.CopyOnWriteArraySet只适用集合频繁迭代但很少修改的情景.