title: Java Date-Time
id: 1499835028593
author: 不识
tags:
  - java
categories:
  - java 基础
  - java 重要对象
date: 2017-07-12 12:50:00
---

***
# Date-Time 概述
***
时间似乎是一个简单的主题;即使是便宜的手表也能提供相当准确的日期和时间。不过，仔细检查一下，你会意识到微妙的复杂性和许多因素，这会影响你对时间的理解。例如，闰年比1月31日增加的结果与其他年份不同。时区也增加了复杂性。例如，一个国家可能在短时间内进出夏令时，或者每年多于一次，或者可能会在一年内完全跳过夏令时。

Date-Time API使用ISO-8601中定义的日历系统作为默认日历。该日历基于公历日历系统，并在全球范围内用作代表日期和时间的标准。Date-Time API中的核心类有有一些比如LocalDateTime，ZonedDateTime和OffsetDateTime等的名字。所有这些都使用ISO日历系统。如果你想要使用其他的日历系统，比如伊历或者泰国佛历，他的java.time.chrono包允许您使用预定义的日历系统之一。或者你可以创建你自己的日历系统。

Date-Time API使用Unicode通用区域设置数据存储库（CLDR）。该存储库支持世界语言，并且包含世界上最大的可用的区域设置数据集。该存储库中的信息已被本地化为数百种语言。Date-Time API还使用时区数据库（TZDB）。该数据库提供自1970年以来有关全球范围内每个时区变化的信息，以及自从引入时区概念依赖，主时区的历史记录。

## Date-Time API历史
***
在Java刚刚发布，也就是版本1.0的时候，对时间和日期仅有的支持就是java.util.Date类。大多数开发者对它的第一印象就是，它根本不代表一个“日期”。实际上，它只是简单的表示一个，从1970-01-01Z开始计时的，精确到毫秒的瞬时点。由于标准的toString()方法，按照JVM的默认时区输出时间和日期，有些开发人员把它误认为是时区敏感的。

在升级Java到1.1期间，Date类被认为是无法修复的。由于这个原因，java.util.Calendar类被添加了进来。悲剧的是，Calendar类并不比java.util.Date好多少。它们面临的部分问题是：

- **可变性**。像时间和日期这样的类应该是不可变的。
- **偏移性**。Date中的年份是从1900开始的，而月份都是从0开始的。
- **命名**。Date不是“日期”，而Calendar也不真实“日历”。
- **格式化**。格式化只对Date有用，Calendar则不行。另外，它也不是线程安全的。

大约在2001年，Joda-Time项目开始了。它的目的很简单，就是给Java提供一个高质量的时间和日期类库。尽管被耽搁了一段时间，它的1.0版还是被发布。很快，它就成为了广泛使用和流行的类库。随着时间的推移，有越来越多的需求，要在JDK中拥有一个像Joda-Time的这样类库。在来自巴西的Michael Nascimento Santos的帮助下，官方为JDK开发新的时间/日期API的进程：JSR-310，启动了。

## Date-Time设计原则
***
新的Date-Time API使用以下几个设计原则来开发

__简洁__   
API中的方法被很好地定义，它们的行为是明确和预期的。例如，使用一个null参数值调用Date-Time方法通常会触发一个NullPointerException。
__流畅__    
Date-Time API提供了一个流畅的接口，使代码简单易读。因为大多数方法不允许具有null值的参数并且不返回null值，方法调用可以链接在一起，所以代码可以被快速理解。比如：
```java
LocalDate today = LocalDate.now();
LocalDate payday = today.with(TemporalAdjusters.lastDayOfMonth()).minusDays(2);
```
__不可变__   
大多数在Date-Time API中的类创建的对象都是不可变的，这意味着在对象创建后，它不可以被修改。要想改变一个不可变对象，只有构建一个新的对象，作为原始对象的修改拷贝副本。这也意味着Date-Time API根据定义是线程安全的。这回影响API，因为用于创建时期和时间对象的方法都是以of,from或with开头的，而不是使用构造方法，并且这里没有set方法。比如：
```java
LocalDate dateOfBirth = LocalDate.of(2012, Month.MAY, 14);
LocalDate firstBirthday = dateOfBirth.plusYears(1);
```
__可拓展__   
Date-Time API是尽可能可扩展的。例如，您可以定义自己的时间调整器和查询，或构建自己的日历系统。

## Date-Time 包
***
Date-time API由主包java.time和四个子包组成：
- java.time   
这个API的核心表示日期和时间。它包含用于日期，时间，日期和时间结合，瞬间，持续时间和时钟的类。这些类基于定义在ISO-8601中的日历系统，并且都是不可变和线程安全的。
- java.tim.chrono  
这个API表示默认ISO-8601以外的日历系统。你同样可以定义自己的日历系统。本教程没有详细介绍这个包。
- java.time.format   
用于格式化和解析日期与时间的类。
- java.time.temporal    
拓展API，主要面向框架和库的编写者，允许日期和时间类之间的互操作，查询和调整。字段（TemporalField和ChronoField）和单位（TemporalUnit和ChronoUnit）在此包中定义。
- java.time.zone
支持时区，时区偏移和时区规则的类。如果使用时区，大多数开发人员只需要使用ZonedDateTime和ZoneId或ZoneOffset。