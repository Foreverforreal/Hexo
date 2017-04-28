title: java 集合框架(十七) 对象排序
id: 1493265180995
author: 不识
date: 2017-04-27 11:53:12
categories:
  - java集合框架
tags:
  - java
  - 集合
---
# 元素排序
***
看下面例子
``` java

	List<String> list =new ArrayList<String>();
	list.add("e");
	list.add("d");
	list.add("a");
	list.add("g");
	list.add("b");
	list.add("f");

	Collections.sort(list);
	System.out.println(list);
        
```
控制台将打印
>[a, b, d, e, f, g]

　之所以Collections.sort(list)能对list中的元素进行排序,是因为String实现了Comparable接口.Comparable接口为一个类提供了自然排序方法,这样这个对象就可以自动的进行排序.下面这些java中重要的类都实现了Comparable接口.
 <!-- more -->
 
 |类|自然排序|
 |--|--------|
|Byte|	Signed numerical|
|Character|	Unsigned numerical|
|Long|	Signed numerical|
|Integer|	Signed numerical|
|Short|	Signed numerical|
|Double|	Signed numerical|
|Float|	Signed numerical|
|BigInteger|	Signed numerical|
|BigDecimal|	Signed numerical|
|Boolean|	Boolean.FALSE < Boolean.TRUE|
|File|	System-dependent lexicographic on path name|
|String|	Lexicographic|
|Date|	Chronological|
|CollationKey|	Locale-specific lexicographic|

***
# Comparable
***
```java
public interface Comparable<T> {
	public int compareTo(T o);
}
```
Comparable只有一个方法compareTo(T)用来与其他对象比较,它的返回值有三种可能  
- 相等时返回0  
- 大于目标对象返回正数  
- 小于目标对象返回负数 

如果目标对象无法与接收的对象比较的话,则会抛出ClassCastException.

我们可以自己写一个实现Comparable的类
```java

class Person implements Comparable<Person> {
        private String name;
        private int age;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getAge() {
            return age;
        }

        public void setAge(int age) {
            this.age = age;
        }

        @Override
        public String toString() {
            return "Person{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    '}';
        }

        @Override
        public int compareTo(Person p) {
            if (p == null && p.getName() == null)
                throw new NullPointerException();
					
            //按照姓名，年龄的顺序进行比较排序        
            int nameCmp = this.name.compareTo(p.getName());
            return nameCmp == 0 ? this.age - p.getAge() : nameCmp;
        }
    }
```
***
# Comparator
***
在List接口定义的方法中，也有一个排序方法sort(Comparator),这个方法并不需要元素对象自己本身实现Comparable接口，而是由集合提供一个排序器Comparator，对元素进行排序。
``` java
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);
   ....
```
Comparator是一个函数式接口，compare方法的两个参数就是需要进行比较的两个元素，通常我们使用以下写法对元素进行排序

``` java 
   
			Person p1 = new Person("zhangsan", 14);
        Person p2 = new Person("zhangsan", 15);
        Person p3 = new Person("lisi", 16);

        List<Person> list = new ArrayList();
        list.add(p1);
        list.add(p2);
        list.add(p3);
			//使用匿名内部类方式
        list.sort(new Comparator<Person>() {
            @Override
            public int compare(Person o1, Person o2) {
                int nameCmp = o1.getName().compareTo(o2.getName());
                return nameCmp == 0 ? o1.getAge() - o2.getAge() : nameCmp;
            }
        });
			//使用lambda表达式
        list.sort((o1, o2) -> {
            int nameCmp = o1.getName().compareTo(o2.getName());
            return nameCmp == 0 ? o1.getAge() - o2.getAge() : nameCmp;
        });

        System.out.println(list);
    }

```

控制台输出

>[Person{name='lisi', age=16}, Person{name='zhangsan', age=14}, Person{name='zhangsan', age=15}]

可见Person 排序先按照name字母顺序排序，如果相同的话，再然后按照年龄大小进行排序。