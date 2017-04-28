title: java集合框架（二）Iterable接口
id: 1493357522560
author: 不识
date: 2017-04-28 13:33:04
categories:
  - java集合框架
tags:
  - java
  - 集合
---
　　Iterable接口是java 集合框架的顶级接口,实现此接口使集合对象可以通过迭代器遍历自身元素,我们可以看下它的成员方法

|修饰符和返回值|方法名|描述|
|--------------|------|----|
|Iterator&lt;T&gt;|***iterator***()|返回一个内部元素为T类型的迭代器|
|default void|***forEach***(Consumer<? super T> action)|对内部元素进行遍历,并对元素进行指定的操作|
|default Spliterator&lt;T&gt;|***spliterator***()|创建并返回一个可分割迭代器|
<!-- more -->

　　Iterable最早出现在JDK 1.5,开始只有iterator()一个抽象方法,需要子类来实现一个内部迭代器Iterator遍历元素.后两个方法是Java 8后新添加的,forEach(Consumer action)是为了方便遍历操作集合内的元素,spliterator()则提供了一个可以并行遍历元素的迭代器,以适应现在cpu多核时代并行遍历的需求.

　　其中我们可以看下default修饰符,这也是Java 8后新出现的,我们知道,如果我们给一个接口新添加一个方法,那么所有他的具体子类都必须实现此方法,为了能给接口拓展新功能,而又不必每个子类都要实现此方法,Java 8新加了default关键字,被其修饰的方法可以不必由子类实现,并且由dafault修饰的方法在接口中有方法体,这打破了Java之前对接口方法的规范.

　　下面是使用迭代器通常写法
``` java
public class IterableTest {
    public static void main(String[] args) {
        iteratorCase();
    }
    public static void iteratorCase(){
        List<Integer> list=new ArrayList<Integer>();
        list.add(1);
        list.add(2);
        list.add(3);
        list.add(4);

        Iterator<Integer> iterator=list.iterator(); //获取ArrayList内部迭代器
        while(iterator.hasNext()){            //hasNext()方法判断是否还有元素
            System.out.println(iterator.next()); //next()返回当前元素,并且将指针移向下个元素
        }
    }
}
```
此外我们还可以使用"for-each loop"形式进行遍历,增强for形式在Java中只是一个语法糖,实际编译的时候,还是会转换为迭代器形式,上面方法体可以改成
```java
for (Integer integer : list) {
         System.out.println(integer);
     }
```
进行迭代遍历的时候我们需要注意这种情况,就是在遍历的过程中,如果我们对元素进行添加删除,那么会造成并行修改异常(ConcurrentModificationException),如下
```java
Iterator<Integer> iterator = list.iterator();
        while (iterator.hasNext()) {
            Integer i = iterator.next();
            if (i == 2) {
                list.remove(i);
            }
        }
```
对于这种情况,,我们应当使用迭代器Iterator内部的remove()方法,而不是使用集合list直接删除元素,正确写法为
```java
Iterator<Integer> iterator = list.iterator();
        while (iterator.hasNext()) {
            Integer i = iterator.next();
            if (i == 2) {
                iterator.remove();  //使用迭代器进行删除元素,注意这里remove()没有参数,它是直接删除当前迭代的元素
            }
        }
```
如果我们自己想自己写一个集合,实现Iterable接口,并可以使用"for-each loop"形式遍历,那么我们需要自己来重写一个迭代器(Iterator)并返回它,看下面代码
```java
public class MyCollection<E> implements Iterable<E> {
    @Override
    public Iterator iterator() {
        return new MyIterator();
    }

    private class MyIterator implements Iterator<E>{
        @Override
        public boolean hasNext() {
            return false;
        }
        @Override
        public E next() {
            return null;
        }
    }
}
```
　这样就可以使用"for-each loop"的形式进行遍历