title: java集合框架（二十一）聚合操作
id: 1493469744611
author: 不识
tags:
  - java
  - 集合
categories:
  - java集合框架
date: 2017-04-29 20:42:00
---
# 聚合操作（Aggregate Operations）
***

通常我们使用集合，不仅仅是存取数据，更多的情况是对集合内的数据进行一定的检索操作。在java 8之前，如果我们要对集合内元素进行复杂的操作，比如我们有一个存储个人信息的集合roster，里面存储着许多Person
```java
public class Person {

    public enum Sex {
        MALE, FEMALE
    }

    String name;
    int age;
    Sex gender;
    
    public int getAge() {
        // ...
    }
    public String getName() {
        // ...
    }
}

```
<!-- more -->
我们要计算这个花名册内所有男性的平均年龄，可能会这么写
``` java

			long sumAge = 0;
			int count = 0;
        
			for (Person person : roster) {
				if(person.getGender() == Person.Sex.MALE){
					sumAge = sumAge + person.getAge();
					count++;
				}
			}
        
			double averageAge = (double)sumAge/count;
        
```
如果我们使用聚合操作的话，代码将会变得简洁明了，如下
```java 
        double average = roster
                .stream()
                .filter(p -> p.getGender() == Person.Sex.FEMALE)
                .mapToInt(Person::getAge)
                .average()
                .getAsDouble();                           
```
上面例子可以展示聚合操作对于集合数据处理的高效与便捷性，在现实应用中，以往传统的J2EE应用，Java代码常常需要依赖（RDBMS）关系型数据库的操作来完成一系列的业务处理，而当脱离RDBMS时，我们需要使用iterator来遍历集合，并进行一系列的比较筛查，现在依靠集合操作，则相当便利。


对于聚合操作，我们需要明白两个概念：**流**（Stream）和**管道**（Pipelines）。
## 流（Stream）
一个流是一序列的元素，但是和集合不同，流不是存储元素的数据结构,而是用来在管道中运载来自数据源的元素。流是绝对不会修改自己底层所封装的数据的，对流的操作只会产生一个新的流。  
对于Collection集合有两个获取流对象的方法
- stream() 获取普通流对象
- parallelStream() 获取并行流用，在并行运算中使用 

>Map集合中并没有stream()方法的存在，但是我们可以通过Map的集合视角获得的Collection间接的对Map进行聚合操作

## 管道（Pipelines） 
一个管道是指一序列的聚合操作，对于一个管道，它包含以下几个部分

- **一个数据源（source）**：数据源可能是集合（collection）或数组（array）或者是一个I/O通道
- **零个或多个中间操作（intermediate operations）**：一个中间操作会产出一个新的流，交给下个中间操作或者终端操作处理。
- **一个终端操作（terminal operation）**：终端操作最后必定产生一个非流形式的结果，比如一个原始类型的值，或集合，或者没有返回值。

管道里中间操作是可以有多个的，但是需要注意的是这么多中间操作是懒（lazy）执行的，意思是调用中间操作的时候，并不会真正遍历操作流，而是等到调用终端操作时，在一次遍历中，执行所有的中间操作，我们可以理解为中间cuo'z。

对于例子中代码来说，roster是数据源，通过stream()获取了流对象，通过一系列的fileter，mapToint中间操作来过滤检索元素，最后通过average是终端操作返回了一个double类型的原始类型数据。

## 与迭代的差异
对于聚合操作，像forEach方法，和迭代看起来很类似，但是聚合操作与迭代有一些根本上的差异。
- **使用内部迭代**：聚合操作对元素的遍历迭代是通过内部委托的方式，由你来指定需要迭代的集合，而迭代方式由JDK来决定。而外部迭代这两者都需要你的代码来决定。这样的话，外部迭代只能按序顺迭代集合元素，而内部迭代则不受这个限制，这样可以更好的利用并行计算，JDK会在内部帮我们分割问题，分线程处理，并自动合并最终的结果。
- **像流一样处理元素**：聚合操作不直接处理集合本身，而是从流中处理元素，因此它有时也被称为流操作
- **支持参数行为**：你可以指定lambda表达式作为参数，这样可以自己定制一些特殊聚合操作行为
***
# Stream
***
从理解意义上来说，聚合操作可以看成包含数据的流在管道内的流动的过程，但是就代码形式来言，聚合操作是集合获取的Stream对象调用自身的一系列方法，处理元素的过程。而这一系列的操作我们在上文中称为管道，我们可以依据管道的操作类型来划分下Stream中常用的方法。
- 中间操作（intermediate operations）  
map (mapToInt, flatMap 等)、 filter、 distinct、 sorted、 peek、 limit、 skip、 parallel、 sequential、 unordered
- 终端操作（terminal operation）  
forEach、 forEachOrdered、 toArray、 reduce、 collect、 min、 max、 count、 anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 iterator
- 短回路（short-circuting）
anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 limit

## 映射（map）
map的作用遍历每个元素，执行一定操作后，映射到一个新的流中，比如
```java 
worList.stream().map( String::toUpperCase )
```



## 参考 
***
 >[Java 8 中的 Streams API 详解](https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/)  
 