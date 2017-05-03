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
- **一个终端操作（terminal operation）**：终端操作最后必定产生一个非流形式的结果，返回要给结果或者副作用（side-effect），需要注意的是终端操作将会消耗掉流，所以一个管道只可能有一个终端操作。

管道里中间操作是可以有多个的，但是需要注意的是这么多中间操作是懒（lazy）执行的，意思是调用中间操作的时候，并不会真正执行，而是等到调用终端操作时，才会真正执行。中间操作我们可以理解成管道的操作配置，程序运行时不是每调用一个中间操作就遍历一遍集合并进行相应的过滤或映射，而是在终端操作执行一次遍历，对每个元素依次按照配置的中间操作，执行相关方法。

对于例子中代码来说，roster是数据源，通过stream()获取了流对象，通过一系列的fileter，mapToint中间操作来过滤检索元素，最后通过average是终端操作返回了一个double类型的原始类型数据。

## 与迭代的差异
对于聚合操作，像forEach方法，和迭代看起来很类似，但是聚合操作与迭代有一些根本上的差异。
- **使用内部迭代**：聚合操作对元素的遍历迭代是通过内部委托的方式，由你来指定需要迭代的集合，而迭代方式由JDK来决定。而外部迭代这两者都需要你的代码来决定。这样的话，外部迭代只能按序顺迭代集合元素，而内部迭代则不受这个限制，这样可以更好的利用并行计算，JDK会在内部帮我们分割问题，分线程处理，并自动合并最终的结果。
- **像流一样处理元素**：聚合操作不直接处理集合本身，而是从流中处理元素，因此它有时也被称为流操作
- **支持参数行为**：你可以指定lambda表达式作为参数，这样可以自己定制一些特殊聚合操作行为
***
# Stream
***
从理解意义上来说，聚合操作可以看成包含数据的流在管道内的流动的过程，但是就代码形式来言，聚合操作是集合获取的Stream对象调用自身的一系列方法，处理元素的过程。而这一系列的操作我们在上文中称为管道.
管道中有中间操作和终端操作两种类型，除此之外，还有一种操作被称为short-circuiting,它用来处理一个无限大的Stream,一个中间操作或者终端操作，可以同时是一个short-circuiting.
- 对于一个 intermediate 操作，如果它接受的是一个无限大（infinite/unbounded）的 Stream，但返回一个有限的新 Stream。
- 对于一个 terminal 操作，如果它接受的是一个无限大的 Stream，但能在有限的时间计算出结果。

依照这些操作，我们可以将Stream一些常用方法分类。
- 中间操作（intermediate operations）  
map (mapToInt, flatMap 等)、 filter、 distinct、 sorted、 peek、 limit、 skip、 parallel、 sequential、 unordered
- 终端操作（terminal operation）  
forEach、 forEachOrdered、 toArray、 reduce、 collect、 min、 max、 count、 anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 iterator
- 短路（short-circuting）
anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 limit

## 中间操作
### map
map的作用遍历每个元素，执行给定操作后，映射到一个新的流中。
```java 
roster.stream().map(Person::getName)
```
获取roster内所有Person的姓名并映射到一个新的流中。

### filter
filter 对原始 Stream 进行某项测试，通过测试的元素被留下来生成一个新 Stream。
```java
   roster.stream().filter(p -> p.getGender() ==Person.Sex.MALE);
```
只保留为男性的Person到一个新的流中

### peek
peek 对每个元素执行操作并返回一个新的 Stream
```java
      roster.stream()
                .filter(p -> p.getGender() ==Person.Sex.MALE)
                .peek(p -> System.out.println(p.getName()))
                .collect(Collectors.toList());

```
这里peek是用来查看我们筛选后peson的姓名，可以用来debug.

### limit/skip
limit 返回 Stream 的前面 n 个元素；skip 则是扔掉前 n 个元素（它是由一个叫 subStream 的方法改名而来）。
```java
public void testLimitAndSkip(){
        List<Person> persons=new ArrayList();
        for(int i=1;i<=10000;i++){
        Person person=new Person(i,"name"+i);
        persons.add(person);
        }
        List<String> personList2=persons.stream().
        map(Person::getName).limit(10).skip(3).collect(Collectors.toList());
        System.out.println(personList2);
        }

private class Person {
    public int no;
    private String name;

    public Person(int no, String name) {
        this.no = no;
        this.name = name;
    }

    public String getName() {
        System.out.println(name);
        return name;
    }
```
输出结果为
>name1
>name2
>name3
>name4
>name5
>name6
>name7
>name8
>name9
>name10
>[name4, name5, name6, name7, name8, name9, name10]

### distinct
distinct是去除流内重复元素
```java
        Integer[] arrays = {1, 1, 2, 2, 3, 3, 4, 4};
        List<Integer> list = new ArrayList(Arrays.asList(arrays));

        List<Integer> distinctList = list.stream()
                .distinct()
                .collect(Collectors.toList());

        System.out.println(distinctList);
```
输出为
>[1, 2, 3, 4]
                                                                 
### sorted
sorted返回一个经过排序后的流，它有两种形式。sorted()或sorted(Comparator),这和集合类似，sorted()需要元素实现Comparable接口，而sorted(Comparator)是我们自己提供排序器。
```java
 List<Person> list = roster.stream()
                .sorted((p1,p2) -> p1.getAge()-p2.getAge())
                .collect(Collectors.toList());
```
例子为将roster中Person按年龄排序后返回一个新的List集合
## 终端操作
### forEach
forEach 方法接收一个 Lambda 表达式，然后在 Stream 的每一个元素上执行该表达式,它与前面的peek功能类似，只是forEach是一个终端操作。
```java
      roster.stream().forEach(p -> System.out.println(p.getName()));
```
打印roster中所有person的姓名
forEach和常规的for循环没有性能上差异，仅仅是函数式风格与传统java风格的差别，所以如果遍历过程中对集合元素进行增删，同样会抛出ConcurrentModificationException异常.
>forEach 不能修改自己包含的本地变量值，也不能用 break/return 之类的关键字提前结束循环。

### findFirst
这是一个 termimal 兼 short-circuiting 操作，它总是返回 Stream 的第一个元素，或者空。需要注意的是findFirst返回的是一个Optional类型值，作为一个容器，它可能含有某值，或者不包含。使用它的目的是尽可能避免 NullPointerException。

```java
Person first = roster.stream().findFirst().get();
```
例子中为获取roster第一个元素

### min/max
min/max是返回流内元素的Optional类型的最小/最大值。
```java
Integer minAge = roster.stream()
	  					.map(Person::getAge)
	  					.min(Integer::max)
	  					.get()
```
例子为获取roster中最大的年龄

### count
count返回流内元素个数

```java
Integer[] array={1,2,3,4,5,6,7};
List<Integer> list = Arrays.asList(array);
long count=list.stream()
				.filter(i -> i>4)
			   .count()
```
输出
>3

### reduce
这个方法的主要作用是把 Stream 元素组合起来。它提供一个起始值（种子），然后依照运算规则（BinaryOperator），和前面 Stream 的第一个、第二个、第 n 个元素组合。如果没有起始值的情况，这时会把 Stream 的前面两个元素组合起来，返回的是 Optional。从这个意义上说，字符串拼接、数值的 sum、min、max、average 都是特殊的 reduce。
```java
roster.stream() 
	  .map(Person::getAge)
    .reduce(0, Integer::sum);
```
上面例子中返回了roster所有人的年龄之和
### collect
collect是一个收集器功能方法，它将流内元素收集到一个容器内，collect也有两种形式，但是通常使用的是与Collectors相结合，
```java
List<Person> list = roster.stream().collect(Collectors.toList());

Map<Person.Sex, List<Person>> byGender =roster.stream().collect(Collectors.groupingBy(Person::getGender));

```
# 并行计算

***
# 参考 
***
 >[Java 8 中的 Streams API 详解](https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/)