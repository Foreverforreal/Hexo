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

通常我们使用集合，不仅仅是存取数据，更多的情况是对集合内的数据进行一定的检索操作。在java 8之前，如果我们要对集合内元素进行复杂的操作，比如我们有一个集合roster，里面存储着许多Person的个人信息
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
如果我们使用聚合操作的话
```java 
        double average = roster
                .stream()
                .filter(p -> p.getGender() == Person.Sex.FEMALE)
                .mapToInt(Person::getAge)
                .average()
                .getAsDouble();
```
这样的话，代码相当简洁明了，聚合操作对于
# 管道与流（Pipelines and Streams）