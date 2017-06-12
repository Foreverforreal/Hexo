title: java 集合框架（十四）Queue
id: 1493364922877
author: 不识
tags:
  - java
  - 集合
categories:
  - java集合框架
date: 2017-04-28 15:35:00
---
# 概述
　　Queue一种队列结构集合,用来存储将要进行处理的元素.通常以FIFO的方式排序元素,但这并不是必须的.比如优先度队列就是一个例外,它是以元素的值来排序.但无论怎样,每个Queue的实现都必须指定它的排序属性.Queue通常不定义元素的equal和hashCode方法.

# 主要方法
　　每个Queue方法都存在两种形式   
   (1)**操作失败则抛出异常**  
   (2)**操作失败返回一个特定值,通常是null或者是false**  

|操作类型|抛出异常|返回特定值|
|--------|--------- |---------|
|**Insert**	|add(e)   |offer(e) |
|**Remove** |remove()  |poll()   |
|**Examine**|element() |peek()   |
<!-- more -->
　　Queue的实现可能会限制集合存储的元素数,这种队列被称为有界队列,对于有界队列当调用add方法时,如果元素数超出其容量限制,就会抛出IllegalStateException异常.offer方法就是专门为有界队列设计的,和add不同的是当插入元素失败的的时候,它返回false而不是抛出异常.

　　Remove和poll方法都是从队列头部弹出元素.具体是那个元素被弹出,这要看队列的排序策略.remove和poll仅当是空集合的时候才有区别,remove会抛出NoSuchElementException,而poll返回null值.

　　Element和peek方法都是返回队列的头部元素,但并不会从队列中将其删除.同remove和poll一样仅当是空集合时两者才有差别,element抛出NoSuchElementException而peek返回null值.

　　Queue的实现通常不允许插入null值,除了LinkedList这个例外,出于历史原因它允许插入null值,但是必须十分注意的是null也是poll和peek方法返回的特别值.

# 实现
　　Queue的实现可以划分为**通用实现**和**并发实现**
 ## 通用实现

　　通用实现主要有两个,一个是**LinkedList**,一个是**PriorityQueue**.LinkedList在List总我们已经说过,它继承自Queue接口,提供FIFO的队列操作形式.  
　　PriorityQueue是一个基于栈结构的优先度队列,它可以根据元素的自然排序或者给定的排序器进行排序.  
## 并发实现
　　在java.util.concurrent包中包含了一系列的同步Queue接口和实类.BlockingQueue继承自Queue,它会在检索元素时等待队列是非空队列处于可用状态,并在存入一个元素后将状态更改为可用状态,下面是它的实现类.

- **LinkedeBlockingQueue**—基于链接节点的可选有界FIFO方式阻塞式队列
- **ArrayBlockingQueue**—基于数组的有界FIFO方式的阻塞式队列
- **PriorityBlockingQueue**—基于栈结构无界阻塞式队列
- **DelayQueue**—基于栈结构的时间调度队列
- **SychronousQueue**—通过使用BlockingQueue接口的简单对接机制的队列 
- **TransferQueue**—在JDK7中 ,TransferQueue是一个特殊额BlockingQueue,它向队列添加一个元素后,可以选择处于等待(阻塞)状态,以让另一个线程检索元素,TransferQueue只有一个实现类
-  **LinkedTransferQueue**—基于链接节点的无界TransferQueue