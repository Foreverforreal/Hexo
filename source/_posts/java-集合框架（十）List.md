title: java 集合框架（十）List
id: 1493363930074
author: 不识
categories:
  - java集合框架
tags:
  - java
  - 集合
date: 2017-04-28 15:19:10
---
# 概述
***
　　List是一种有序集合,有时也被称为序列,可以有重复的元素.List集合相比Collection,除了直接继承的方法外,有以下拓展的操作方法

- **位置访问**---可以基于元素索引来操作元素,比如get,set,add,addAll和remove方法都支持这一点
- **搜索**---在集合中搜索一个特定对象,并返回它的索引,如indexOf和lastIndexOf方法
迭代---除了继承自Collection中的迭代器,List还提供一个基于Iterator拓展的ListIterator迭代器
- **视图**---subList方法提供任意的范围操作方法
　　同Set一样,List也要求强化equal和hashCode以使两个集合元素可以进行逻辑上的比较,而不考虑他们具体实现类的类型.当两个List有相同的元素时,他们被认为是相等的.
  
<!-- more -->
***
# 主要方法
***
|修饰符和返回值|方法名|描述|
|--------------|------|----|
|**添加功能**|||
|boolean|**add**(int,E)|向集合指定索引处插入一个对象,该索引必须与集合连贯|
|boolean|**addAll**(int,Colletion&lt;? extend E&gt;)|向集合指定索引处插入一个集合的所有元素,该索引必须与集合连贯|
|**修改功能**|||
|E|**set**(int,E)|将集合指定索引出元素修改为为指定对象,并返回旧元素|
|boolean|**remove**(int)|移除指定索引处元素,集合元素数没有改变时返回false|
|void|**replaceAll**(UnaryOperator&lt;E&gt;)|按照指定一元运算对集合内所有元素进行修改|
|**获取功能**|||
|Object|**get**(int)|获取指定索引处元素|
|int|**indexOf**(Object)|从集合中查找给定对象第一次出现的索引,没有时返回-1|
|int|**lastIndexOf**(Object)|从集合中查找给定对象最后一次出现的索引,没有时返回-1|
|List&lt;E&gt;|**subList**(int,int)|返回指定索引之间元素组成的视图,包含起始索引元素,不包括结束索引元素|
|**迭代器**|||
|ListIterator&lt;E&gt;|**listIterator**()|获取集合序列迭代器|
|ListIterator&lt;E&gt;|**listIterator**(int)|获取集合序列迭代器,并将指针指向给定索引元素前|
|**排序**|||
|void|**sort**(Comparator&lt;s super E&gt;)|通过给定的排序器进行排序|

　List大多操作元素的方法都与索引有关,所以需要注意可能引起索引越界的问题,add,remove,set,get,subList这些方法,当给定索引大于集合最大索引或者为负数时,都会抛出IndexOutOfBoundsException异常.

>List进行视图操作时,同Set集合类似,任何对视图的修改都会影响到原集合,反之亦然.

***
# List遍历
***
　　List从自Collection继承的Iterator迭代器基础上,还根据List特点拓展提供了一个ListIterator迭代器,ListIterator继承自Iterator,所以它也有hasNext(),next(),remove()这些方法.List在获取ListIterator时,提供了两个方法,分别是listIterator()和listIterator(int),区别是有参数的方法在获取迭代器时,会把迭代器指针预先指向给定的索引元素前.ListIterator不仅可以正向遍历,也可以反向遍历,通过hasPrevious(),previous()两个方法,从方法名可以看出,这两个方法和hasNext(),next()类似,只是操作方向反.ListIterator还提供几个方法nextIndex(),previousIndex(),add(E),set(E).  
　　nextIndex()和previousIndex()分别是获取迭代当前元素的前一个和后一个元素的索引,有这样两种情况需要注意

1. 指针指向集合头部元素前时,previousIndex()返回-1;

2. 指针指向集合尾部元素后时,nextIndex()返回list.size();

add(E)方法会在迭代的时候,向指针移动的方向后面添加一个元素,而set(E)方法将当前迭代的元素修改为给定对象.
***
# 实现
***
　　List的实现也分两种,**通用实现**和**专用实现**

## 通用实现
　　通用实现类主要有两个ArrayList和LinkedList.通常我们拿最多使用的是ArrayList,它对元素访问花费常量时间.但是如果频繁的对一个List头部插入元素,或者遍历集合删除元素的时候应该考虑使用LinkedList.这些操作上,LinkedList花费常量时间,ArrarList花费线性时间,在元素的访问上LinkedList花费线性时间,而ArrayList花费常量时间,这是因为ArrayList底层通过数组结构来实现,当对元素的增删时,需要重新开辟一份内存空间,并拷贝原来的数据,而LinkedList底层是链表结构,每个元素都是一个节点,节点在内存中的存储是无序随机的,但每个节点都包含前一个节点和后一个节点的内存地址,从而实现查找,在增删元素的时候,只需要修改特定节点和其对其他节点的联系.

　　综合考虑,当要使用LinkedList的时候最好测试一下LinkedList和ArrayList的性能,通常ArrayList表现更好些.

　　List还有两个遗留实现Vector,Stack.Vector相比ArrayList是线程安全的,Stack是一种后进先出的数据结构(LIFO),继承自Vector.这两个实现都不推荐使用,他们是java早期版本的遗留,仅为向上兼容,可用Collections提供的静态同步封装器封装ArrayList代替Vector,虽然Vector性能要稍好些,使用Deque的通用实现代替Stack.

## 专用实现

　　List专用实现类主要有CopyOnWriteList,它和CopyOnWriteSet类似,实现读写分离,不需要手动进行同步,适合用来维持一个频繁读取而很少修改的事件处理列表.