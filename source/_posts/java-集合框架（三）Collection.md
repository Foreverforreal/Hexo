title: java 集合框架（三）Collection
id: 1493361277304
author: 不识
categories:
  - java集合框架
tags:
  - java
  - 集合
date: 2017-04-28 14:34:46
---
***
# 概述
***
![Collection](http://www.processon.com/chart_image/58e8a70ae4b01f83d25e1b79.png)
　Collection是集合框架的根接口.不同的集合具有不同的特性,比如有的集合可以有重复元素,有的不可以,有的可以排序,有的不可排序,如此等等,而Collection作为集合的根接口,它规范定义了集合的通用方法,一个集合我们可以看作一个在内存中的小型数据库,而数据库的常用操作无外乎"增删改查",Collection中的方法也大体是这些类型操作.

　　此外Colletion的所有通用实现类都会有一个**转换器构造方法**,它接收一个Collection类型参数,这样可以以另一个Collection类型集合中元素来初始化自己,也相当于实现了集合类型的相互转换.
  
<!-- more -->
***
# 主要方法
***
|修饰符和返回值|方法名|描述|
|--------------|------|----|
|**添加功能**|||
|boolean|**add**(E)	|向集合添加一个元素,集合元素数没有变化的话返回false|
|boolean|**addAll**(Collection<? extends E>)|向集合添加另一个集合全部元素,集合元素数没有变化的话返回false|
|**删除功能**|||
|boolean|**remove**(Object)|从集合移除一个元素,集合元素数没有变化的话返回false|
|boolean|**removeAll**(Colletion<?>)|从集合移除另一个集合中所有元素,集合元素数没有变化的话返回false|
|defalut boolean|**removeIf**(Predicate<? super E>)|按条件从集合中移除相应元素,集合元素没变化的话返回false|
|void|**clear**()| 清空集合内元素|
|boolean|**retainAll**(Collection<?> )|	保留交集元素,移除不在交集中的元素,集合元素数没有变化的话返回false|
|**判读功能**|||
|boolean|**contains**(Object)|	判读对象是否在集合内|
|boolean|**containsAll**(Collection<?>)|	判断参数集合内元素是否都在集合内|
|boolean|**equals**(Object)|	判断集合是否与传入对象"相等"|
|boolean |**isEmpty**()|判断集元素是否为空 |
|**获取功能**|||
|int|**size**()|获取集合内实际元素数|
|Object[]|**toArray**()|返回包含集合所有元素的数组|
|T[]|**toArray**(T[])|返回包含集合所有元素的数组,如果集合元素数大于传入数组长度,返回为新建数组,否则返回为传入数组,传入数组若果有剩余,多余填充为null值,如果数组类型与集合元素类型不符,抛出ArrayStoreException|
|int|**hashCode**()|返回对象哈希值|
|**迭代器**|
|Iterator|**iterator**()|返回集合迭代器|
|defalut SplIteraror|**splIterator**()|	返回集合可分割迭代器|
|**其他**|||
|defalut Stream&lt;E&gt;|**stream**()|返回流对象|
|defalut Stream&lt;E&gt;|**parallelStream**()|	返回并行流对象| 

Colletion接口中定义的方法是集合操作中最通用的操作方法,按照对元素不同的操作类型大致可以分为**添加,删除,判断,获取**这四种,集合为了实现元素的遍历还要提供一个获取迭代器的方法,此外在java 8之后为了应对现在分布式并行操作需求,提供了一个可分割迭代器spliterator(),为了方便开发者对集合元素快速遍历和处理,java 8新提出的聚合操作概念,可以通过stream()和parallelStream()方法来实现聚合操作.

# 集合遍历
　　对于集合的遍历,我们知道Collection继承了Iterable接口.所以可以使用迭代器和for-loop以及forEach()形式进行遍历. 在Java 8之后,我们可以获取集合的流(stream),然后进行聚合操作(Aggregate Operations)遍历,聚合操作在后面文章有详细介绍。
```java

        Collection<String> c = new ArrayList();

        c.add("aad");
        c.add("bde");
        c.add("cdf");
        c.add("dad");

        //使用聚合操作
        c.stream().filter(new Predicate<String>() {
            @Override
            public boolean test(String s) {
                return s.contains("a");
            }
        }).forEach(new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        });
        //与lambda表达式结合
        c.stream().filter(s -> s.contains("b"))
                  .forEach(s -> System.out.println(s));
    }

```

控制台输出为
>aad  
>dad  
>bde  

# 子接口
- **Set**是一种无序而元素唯一的集合类型,它是比如扑克中的卡牌,学生课程表安排的课程,计算机中运行的进程这些事物的数学概念的抽象表示.Set接口相比Colletion并没有更多的操作方法,而他的子接口SortedSet和NavigableSet进行了更多的拓展,可以看出子接口中方法侧重对元素的比较和排序

- **List**是一种有序且元素可重复的集合类型,它像数组一样可以通过索引来快速查找操作元素.

- **Queue**是一种队列结构,它更适合用来存储数据而不是处理数据.Queue常用来做先见先出的(FIFO)的存储结构,新加入的元素会被存储在集合尾部,取出元素则会从头部取出.Deque是一种双向队列,既可以做先进先出(FIFO)也可以做先进后出（LIFO）。