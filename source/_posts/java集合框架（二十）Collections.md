title: java集合框架（二十）Collections
id: 1493102958141
author: 不识
tags:
  - java
  - 集合
categories:
  - java集合框架
date: 2017-04-29 14:49:00
---
# Collections工具类
***
Collections是一个只包含一系列用来操作或返回集合的静态方法的工具类。   
Collections提供了很多的包装器，用来包装一些通用集合，为它们提供额外的功能，包装器通常使用是传入集合的通用实现作为参数，然后返回一个该实现类型的新的集合，如果传入的参数引用为null的话，则会抛出NullPointerException 异常。 
此外Collectios中提供一系列的算法来操作集合，比如进行集合的排序，反转，混排等等，一般这些方法都接受一个集合参数，并且主要是针对List类型的集合，一小部分是Collection类型。
Collections提供的功能可以大体分为以下几种   
<!-- more -->
1. **包装实现（Wrapper Implementations）**
	- 同步包装器（Synchronization Wrappers）
	- 不可变包装器（Unmodifiable Wrappers）
	- 类型检查包装器（Checked Interface Wrappers）      			  
2. **便利实现（Convenience Implementations）**  
	- 不可变多重元素复制List（Immutable Multiple-Copy List）
	- 不可变单元素集合(Immutable Singleton Collections)
	- 不可变空集（Immutable Empty Set, List, and Map）   
3. **集合操作（Collections Operation）**
	- 排序（Sorting）
	- 混排（Shuffling）
	- 查找（Searching）
	- 常规数据操作（Routine Data Manipulation）
	- 组成（Composition）
	- 极值查找（Finding Extreme Values）
***
# 包装实现
***
> Wrapper implementations delegate all their real work to a specified collection but add extra functionality on top of what this collection offers. For design pattern fans, this is an example of the decorator pattern.   

Collections提供的包装器会将实际工作交给传入的指定集合，但是在它上面附加一些额的功能，对于设计模式粉丝来说，这是典型的装饰者模式.

## 同步包装器

集合框架中大部分通用实现集合都是线程不安全的，而同步包装器可以将这些集合包装成线程安全集合。Collections为集合框架六个核心接口Collections,List,Set,Map,SortedSet以及SortedMap都提供了一个静态方法

- public static &lt;T&gt; Collection&lt;T&gt; ***synchronizedCollection***(Collection&lt;T&gt; c);
- public static &lt;T&gt; Set&lt;T&gt; ***synchronizedSet***(Set&lt;T&gt; s);
- public static &lt;T&gt; List&lt;T&gt; ***synchronizedList***(List&lt;T&gt; list);
- public static &lt;K,V&gt; Map&lt;K,V&gt; ***synchronizedMap***(Map&lt;K,V&gt; m);
- public static &lt;T&gt; SortedSet&lt;T&gt; ***synchronizedSortedSet***(SortedSet&lt;T&gt; s);
- public static &lt;K,V> SortedMap&lt;K,V&gt; ***synchronizedSortedMap***(SortedMap&lt;K,V&gt; m);  


这些方法都返回了一个线程安全的集合，为了保证串行存取，所有对传入的指定集合的访问都必须通过方法返回的集合来完成。因此最好的做法是不保持对传入集合的引用，如下文写法

```java
List<Type> list = Collections.synchronizedList(new ArrayList<Type>());
```
需要注意的是，在并发访问的时候，如果对同步包装器返回的集合进行迭代，需要在迭代临界区加上同步代码块，因为迭代是通过多次调用集合来完成的

```java
Collection<Type> c = Collections.synchronizedCollection(myCollection);
synchronized(c) {
    for (Type e : c)
        foo(e);
}
```
使用同步包装器一个小的缺点是，你没有办法调用非接口实现的方法，例如在前面例子中list虽然是包装的ArrayList集合，但是它无法调用ArrayList的ensureCapacity方法。

## 不可变包装器
和同步包装器不同的是，不可变包装器是限制了集合的一些操作，比如增删元素，调用被限制的操作，则会抛出UnsupportedOperationException异常。不可变包装器主要有两个用途
* 确使集合在创建时就不可变。在这种用途下，最好不要保持对传入集合的引用，这样可以绝对保证集合的不可变性。
* 使客户端可以以只读方式访问你的数据结构。你可以保持对传入集合的引用，同时分发返回集合给客户端，这样客户端只看数据而不能修改，但是你亦然保留对数据的所有操作权限。

同同步包装器一样，不可变包装器对集合框架的六个核心接口都提供了一个静态工厂方法

- public static &lt;T&gt; Collection&lt;T&gt; ***unmodifiableCollection***(Collection&lt;T&gt; c);
- public static &lt;T&gt; Set&lt;T&gt; ***unmodifiableSet***(Set&lt;T&gt; s);
- public static &lt;T&gt; List&lt;T&gt; ***unmodifiableList***(List&lt;T&gt; list);
- public static &lt;K,V&gt; Map&lt;K,V&gt; ***unmodifiableMap***(Map&lt;K,V&gt; m);
- public static &lt;T&gt; SortedSet&lt;T&gt; ***unmodifiableSortedSet***(SortedSet&lt;T&gt; s);
- public static &lt;K,V> SortedMap&lt;K,V&gt; ***unmodifiableSortedMap***(SortedMap&lt;K,V&gt; m);  

## 类型检查包装器
类型检查包装器是提供给泛型集合的。它会返回一个给定集合的动态类型安全视图，如果我们向返回的视图里添加一个错误类型的元素，就会抛出ClassCastException异常。

类型检查包装器提供了对之前文章中介绍的所有接口提供了一个静态工厂方法，其方法形式为

- public static &lt;E&gt; Collection&lt;E&gt; ***checkedCollection *** (Collection&lt;E&gt; c,Class&lt;E&gt; type)

之所以提供这个功能，我们知道泛型机制是在编译时期，对集合元素类型进行静态检查检查，但是这样的话，如果在运行时期添加元素，或者将泛型集合引用传递给另一个集合，则泛型机制就会失效，比如下面的例子
``` java

			List<String> commonList = new ArrayList();
			List<String> checkedList = Collections.checkedList(new ArrayList(),String.class);
            
			commonList.add("string");
			checkedList.add("string");

			List newCommonList1= commonList;
			List newCheckedList2= checkedList;

			newCommonList1.add(1);
			System.out.println(commonList); //控制台输出[string, 1]
			newCheckedList2.add(1);         //抛出ClassCastException异常
			System.out.println(checkedList);
        
```
***
# 便利实现
***
## 不可变多重复制元素List
有时我们可能需要一个包含重复单一元素不可变的List集合，Collections.nCopies方法就返回一个这样的List.这种实现主要有两种用法
- 初始化一个新创建的List
- 扩展一个已经存在的List

比如，我们需要要给包含10个“null”类型的List

``` java 
List<Type> list = new ArrayList<Type>(Collections.nCopies(100, (Type)null);
```
或者向一个已经存在的集合添加重复元素,下面例子演示，向一个字母表List中添加10个“a”元素

``` java
alphabetList.addAll(Collections.nCopies(10, "a"));
```

## 不可变单元素集合
有时我们可能需要一个不可变的单元素集合，它只包含一个给定的元素。Collections中分别为为Set,List,Map三种集合提供了此类实现的静态方法
- public static &lt;T&gt; Set&lt;T&gt; ***singleton***(T o)  
- public static &lt;T&gt; List&lt;T&gt; ***singletonList***(T o)  
- public static &lt;K,V&gt; Map&lt;K,V&gt; ***singletonMap***(K key,V value)

这种实现主要用来作为一个方法的参数出传递，比如一个方法只接受集合类型参数，而这个集合参数只有一个元素，那我们就可以使用这个这些方法，而不必自己初始化一个集合，一个典型的例子是我们要要从一个集合中删除全部的给定元素
``` java 
c.removeAll(Collections.singleton(e));
```

## 不可变空集
Collections还提供了返回空集的静态方法和字段常量

字段
- public static final List **EMPTY\_LIST**
- public static final Set **EMPTY\_SET**  
- public static final Map **EMPTY\_MAP** 

方法
- public static final &lt;T&gt; List&lt;T&gt; ***emptyList***();
- public static fibal &lt;T&gt; Set&lt;T&gt; ***emptySet***();
- public static final &lt;T&gt; List&lt;T&gt; ***emptyMap***();
> - 可以看出方法相比提供的常量，只是添加了泛型机制   
> - 这些空集都是不可变的，执行元素的添加操作会抛出UnsupportedOperationException异常

***
# 集合操作
***
## 排序
Collections提供了两个排序方法
- sort(List&lt;T&gt; list)
- sort(List&lt;T&gt; list,  Comparator<? super T> c)

Collections的排序方法可以将List集合内元素按自然排序方式，或者给定的排序器进行排序，这在[java 集合框架(十七) 对象排序]()已经介绍过

## 混排

Collections提供了两个混排方法
 - shuffle(List<?> list)
 - shuffle(List<?> list,Random rnd)   
  
shuffle(List)将传入的List集合内元素随机打乱排序。而shuffle(List,Random)则还需要一个Random作为随机源，其实shuffle(List)也是调用shuffle(List,Random)方法，只是使用了默认的Random对象，以系统时间作为Random的随机种子。

## 查找

Collections提供了二分查找算法查找元素二分查找法会从集合中间元素开始，按照自然排序的规则开始对比，如果集合中间元素大于给定元素，则向集合前移动继续对比，如果小于给定元素，则向后移动继续对比，直到找到相同元素为止。所以按照这种查找方法，集合必须事先按升序排序好，否则查找结果是无法预知的，而且如果查找的元素在集合内有多个结果，也无法保证哪一个会被先找到。  
Collections依此提供了两个静态方法
- binarySearch((List<? extends Comparable<? super T>> list, T key))
- binarySearch(List<? extends T> list, T key, Comparator<? super T> c)

第一个方法，假定了提供的List已经按照升序排序好，而第二种则需提供一个排序器，来先按升序排序集合，然后进行查找。如果找到元素，则返回其索引值，如果给定元素不在集合中的话，则返回(-(insertion point) - 1)，insertion point指的是如果要向集合中以升序排序方式插入该给定的元素，它的插入位置。

如下面例子
``` java 

        List<Integer> list = new ArrayList();

        list.add(2);
        list.add(3);
        list.add(4);

        for (int i = 0; i< 7; i++){
            System.out.println("specify value"+i+"return value:   "+Collections.binarySearch(list,i));
        }

```

控制台输出
>specify value  0  return value:   -1  
>specify value  1  return value:   -1  
>specify value  2  return value:   0  
>specify value  3  return value:   1  
>specify value  4  return value:   2  
>specify value  5  return value:   -4  
>specify value  6  return value:   -4  

## 常规数据操作

Collections提供了五种对List集合常规数据操作的算法。
- **reverse**----反转List元素的顺序
- **fill**----用给定的值重写List中的每个元素
- **copy**----将源List集合中元素复制并重写到目标List集合中，必须要保证目标集合**实际元素个数**大于源集合，如果有剩余空间，那么目标集合剩余元素不受影响
- **swap**----交换List集合中指定两个位置元素的值
- **addAll**----将指定的所有元素都添加到一个Collection类型集合中。

下面用实际代码演示

```java


        List<Integer> list = new ArrayList();
        list.add(1);
        list.add(2);
        list.add(3);

        //添加元素
        Collections.addAll(list,4,5);
        System.out.println(list);

        //反转集合
        Collections.reverse(list);
        System.out.println(list);

        //拷贝集合
        List<Integer> list1 = new ArrayList();
        for (int i=0;i<7;i++){
            list1.add(0);
        }
        Collections.copy(list1,list);
        System.out.println(list1);


        //对换元素
        Collections.swap(list,0,4);
        System.out.println(list);

        //填充元素
        Collections.fill(list,null);
        System.out.println(list);     

```

控制台输出为
>[1, 2, 3, 4, 5]  
>[5, 4, 3, 2, 1]  
>[5, 4, 3, 2, 1, 0, 0]  
>[1, 4, 3, 2, 5]  
>[null, null, null, null, null]

## 组成

Collections提供了频率（frequency ）和不相交（disjoint ）两种算法，来测试一个或多个集合的组成
- frequency(Collection<?> c,Object o)
- disjoint(Collection<?> c1,Collection<?> c2)

frequency可以统计给定对象在集合中出现的次数,如果元素不在集合中的话则返回0；
disjiont是判断给定的两个集合是否有交集，没有的话则返回true。

## 极值查找

Collections可以查找集合中的最大值最小值，提供了一下四个方法

- **min**(Collection<? extends T> coll)
- **min**(Collection<? extends T> coll,Comparator<? super T> comp)
- **max**(Collection<? extends T> coll)
- **max**(Collection<? extends T> coll,Comparator<? super T> comp)   

极值查找的方法需要集合的元素实现Comparable接口，按照自然排序的方式对比元素，如果元素没有继承Comparable接口，还可以使用第二种方式，在方法中我们自己提供一个排序器，对元素进行比较。