title: java 集合框架（十五）Deque
id: 1497233597835
author: 不识
tags:
  - java
  - 集合
categories:
  - java集合框架
date: 2017-04-28 15:40:00
---


# 概述
　　Deque是Queue的子接口,我们知道Queue是一种队列形式,而Deque则是双向队列,它支持从两个端点方向检索和插入元素,因此Deque既可以支持LIFO形式也可以支持LIFO形式.Deque接口是一种比Stack和Vector更为丰富的抽象数据形式,因为它同时实现了以上两者.

# 主要方法

|修饰符和返回值|方法名|描述|
|--------------|------|----|
|**添加功能**|||
|void|**push**(E)|向队列头部插入一个元素,失败时抛出异常 |
|void|**addFirst**(E)|向队列头部插入一个元素,失败时抛出异常|
|void|**addLast**(E)|向队列尾部插入一个元素,失败时抛出异常|
|void|**offerFirst**(E)|向队列头部加入一个元素,失败时返回false|
|void|**offerLast**(E)|向队列尾部加入一个元素,失败时返回false|
|**获取功能**|||
|E|**getFirst**()|获取队列头部元素,队列为空时抛出异常|
|E|**getLast**()|获取队列尾部元素,队列为空时抛出异常|
|E|**peekFirst**()|获取队列头部元素,队列为空时返回null|
|E|**peekLast**()|获取队列尾部元素,队列为空时返回null|
|**删除功能**|||
|boolean|**removeFirstOccurrence**(Object)|删除第一次出现的指定元素,不存在时返回false|
|boolean|**removeLastOccurrence**(Object)|删除最后一次出现的指定元素,不存在时返回false|
|**弹出功能**|||
|E|**pop**()|弹出队列头部元素,队列为空时抛出异常|
|E|**removeFirst**()|弹出队列头部元素,队列为空时抛出异常|
|E|**removeLast**()|弹出队列尾部元素,队列为空时抛出异常|
|E|**pollFirst**()|弹出队列头部元素,队列为空时返回null |
|E|**pollLast**()|弹出队列尾部元素,队列为空时返回null |
|**迭代器**|||
|Iterator&lt;E>|**descendingIterator**()|返回队列反向迭代器|

可以看出Deque在Queue的方法上新添了对队列头尾元素的操作,add,remove,get形式的方法会在有界队列满员和空队列时抛出异常,offer,poll,peek形式的方法则会返回false或null.

此外方法表中需要注意push = addFirst,pop = removeFirst,只是使用了不同的方法名体现队列表示栈结构时的特点.


# 实现
　　同Queue一样Deque的实现也可以划分成**通用实现**和**并发实现**.

　　通用实现主要有两个实现类ArrayDeque和LinkedList.

　　ArrayDeque是个可变数组,它是在Java 6之后新添加的,而LinkedList是一种链表结构的list.LinkedList要比ArrayDeque更加灵活,因为它也实现了List接口的所有操作,并且可以插入null元素,这在ArrayDeque中是不允许的.

　　从效率来看,ArrayDeque要比LinkedList在两端增删元素上更为高效,因为没有在节点创建删除上的开销.最适合使用LinkedList的情况是迭代队列时删除当前迭代的元素.此外LinkedList可能是在遍历元素时最差的数据结构,并且也LinkedList占用更多的内存,因为LinkedList是通过链表连接其整个队列,它的元素在内存中是随机分布的,需要通过每个节点包含的前后节点的内存地址去访问前后元素.

　　总体ArrayDeque要比LinkedList更优越,在大队列的测试上有3倍与LinkedList的性能,最好的是给ArrayDeque一个较大的初始化大小,以避免底层数组扩容时数据拷贝的开销.

　　LinkedBlockingDeque是Deque的并发实现,在队列为空的时候,它的takeFirst,takeLast会阻塞等待队列处于可用状态




