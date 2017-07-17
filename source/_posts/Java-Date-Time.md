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

## 方法命名约定
Date-Time API在丰富的类中提供了丰富的方法。并且尽可能在类之间使方法名称一致。比如，许多类提供一个now方法，它捕获与该类相关的当前时刻的日期或时间值还有一个from方法，它允许从一个类转换为另一个类。

还有关于方法名称前缀的标准化。因为Date-Time API中的大多数类是不可变的，所以API不包括set方法。（在创建之后，一个不可变对象的值不能被改变。不可变对象等同于set方法的是with方法）下面表格列举了通常使用的前缀。

|前缀|方法类型|使用|
|----|--------|----|
|of|静态工厂|创建一个实例，其中工厂主要验证输入参数，而不转换它们。|
|from|静态工厂|将输入参数转换为目标类的实例，这可能涉及从输入中丢失信息。|
|parse|静态工厂|解析输入字符串来产生一个目标类的实例|
|format|实例|使用指定的formater来格式化事件对象中的值，产生一个字符串|
|get|实例|返回目标对象状态的一部分|
|is|实例|查询目标对象的状态|
|with|实例|返回一个目标对象修改了一个元素的副本。这是不可变对象等同于JavaBean中set方法的方法。|
|plus|实例|返回目标对象增加了一段时间的副本|
|minus|实例|返回目标对象扣除了一段时间的副本|
|to|实例|将这个对象转换为另一种类型|
|at|实例|将这个对象与另一个组合在一起|
***
# 标准日历
***
Date-Time API的核心是java.time包。定义在java.time中的类的它们的日历系统是基于ISO日历，它是代表日期和时间的世界标准。ISO日历遵循前公历规则。公历日历在1582年引入；在前公历日历中，日期从那时起向后延伸，以创建一致，统一的时间线，并简化日期计算。
## 概述
***
本节比较人类时间和机器时间的概念，提供了java.time包中主要的基于时间的类的表。
     
代表时间有两种基本方式。一种是以人为本代表时间的方式，称为人类时间，如年，月，日，时，分，秒。另一种方式是机器时间，以一个毫秒的分辨率，从一个起源的时间线不断地测量时间，被称为时代。Date-Time包提供了丰富的类来代表日期和时间。在Date-Time API中一些类旨在表示机器时间，而另一些则更适合代表人类时间。

首先确定您需要哪些方面的日期和时间，然后选择满足这些需求的类。当你选择基于时间的类后，你首先决定是否需要代表人类时间还是机器时间。然后，您将确定您需要代表时间的哪些方面。你是需要一个时区？日期和时间？只是日期？如果你需要一个日期，你是需要月，天，年，还是子集？
> 术语：在Date-Time API中捕获并使用日期或时间的值的类，比如Instant，LocalDateTime和ZonedDateTime，在本教程中被称为基于时间的类（或类型）。支持类型，如TemporalAdjuster接口或DayOfWeek枚举，不包括在此定义中。

比如，你可能使用一个LocalDate对象来表示生日日期，因为大多数人注意它们的生日，无论他们是在他们的出生城市还是跨过全球，在国际日期变更线的另一边。如果您正在追踪占星术时间，那么您可能希望使用LocalDateTime对象来表示出生日期和时间，或者ZonedDateTime，它还包括时区。如果你创建一个时间戳，那么你最可能想要使用一个Instant，它允许您将时间轴上的一个瞬时点与另一个瞬时点进行比较。

下面的表格总结了java.time包中的基于时间的类，它们存储了日期与/或时间的信息，或者可以用来衡量一段时间。在列中的对号指示该类使用该特定类型的数据，并且toString Output列显示使用toString方法打印的实例。Where Discussed列链接到本教程相应的位置。

<style>
table th:first-of-type {
    width: 95px;
}
table th:nth-of-type(2),table th:nth-of-type(3),table th:nth-of-type(4),table th:nth-of-type(5),table th:nth-of-type(6),table th:nth-of-type(7),table th:nth-of-type(8),table th:nth-of-type(9){
    width: 30px;
}
</style>

|类或枚举|年|月|日|小时|分钟|秒|时区偏移|时区ID|toString Output|Where Discussed|
|--------|--|--|--|----|----|--|--------|------|---------------|---------------|
|Instant||||||√|||2013-08-20T15:16:26.355Z|[Instant Class](/#Instant)|
|LocalDate|√|√|√||||||	2013-08-20||
|LocalDateTime|√|√|√|√|√|√|||2013-08-20T08:16:26.937||
|ZonedDateTime|√|√|√|√|√|√|√|√|2013-08-21T00:16:26.941+09:00[Asia/Tokyo]||
|LocalTime||||√|√|√|||08:16:26.943||
|MonthDay||√|√||||||--08-20||
|Year|√||||||||2013||
|YearMonth|√|√|||||||2013-08||
|Month||√|||||||AUGUST||
|OffsetDateTime|√|√|√|√|√|√|√||	2013-08-20T08:16:26.954-07:00||
|OffsetTime||||√|√|√|√||	08:16:26.957-07:00||
|Duration|||\*\*|\*\*|\*\*|√|||PT20H (20 hours)||
|Period|√|√|√||||\*\*\*|\*\*\*|P10D (10 days)|||

\*秒是获得的毫秒精度
\*\*这类别不存储这些信息，但是具有在这些单元中提供时间的方法。
\*\*\*当将Period添加到ZonedDateTime时，会观察到夏令时或其他本地时差。

## DayWeek和Month枚举
本节讨论定义一周中的日期的枚举（DayWeek）和定义了月的枚举（Month）。

Date-Time API提供了指定一周中的日期和一年中的月份的枚举。
### DayWeek
DayOfWeek枚举包含七个常数，用于描述星期几：从MONDAY到SUNDAY。DayOfWeek常数的整数值范围从1（Monday）到7（Sunday）。使用定义的常量（DayOfWeek.FRIDAY）使您的代码更易读。

这个枚举还提供一些方法，类似基于时间类提供的方法。比如，下面的代码向“Monday”添加3天，并且打印结果。输出是“THURSDAY”：
```java
System.out.printf("%s%n", DayOfWeek.MONDAY.plus(3));
```
通过使用*getDisplayName(TextStyle, Locale) *方法，你可以以用户区域来检索标识一周中星期几的字符串。TextStyle枚举使您能够指定要显示的字符串类型：FULL,NARROW（通常是单个字母），或者SHORT（一个缩写）。STANDALONE TextStyle常量在某些语言中使用，其输出在用作日期的一部分时不同于自身使用时的输出。
## Date Classes

This section shows the temporal-based classes that deal only with dates, without time or time zones. The four classes are LocalDate, YearMonth, MonthDay and Year.

Date and Time Classes

This section presents the LocalTime and LocalDateTime classes, which deal with time, and date and time, respectively, but without time zones.

Time Zone and Offset Classes

This section discusses the temporal-based classes that store time zone (or time zone offset) information, ZonedDateTime, OffsetDateTime, and OffsetTime. The supporting classes, ZoneId, ZoneRules, and ZoneOffset, are also discussed.

Instant Class

This section discusses the Instant class, which represents an instantaneous moment on the timeline.

Parsing and Formatting

This section provides an overview of how to use the predefined formatters to format and parse date and time values.

The Temporal Package

This section presents an overview of the java.time.temporal package, which supports the temporal classes, fields (TemporalField and ChronoField) and units (TemporalUnit and ChronoUnit). This section also explains how to use a temporal adjuster to retrieve an adjusted time value, such as "the first Tuesday after April 11", and how to perform a temporal query.

Period and Duration

This section explains how to calculate an amount of time, using both the Period and Duration classes, as well as the ChronoUnit.between method.

Clock

This section provides a brief overview of the Clock class. You can use this class to provide an alternative clock to the system clock.

Non-ISO Date Conversion

This section explains how to convert from a date in the ISO calendar system to a date in a non-ISO calendar system, such as a JapaneseDate or a ThaiBuddhistDate.

Legacy Date-Time Code

This section offers some tips on how to convert older java.util.Date and java.util.Calendar code to the Date-Time API.

Summary

This section provides a summary of the Standard Calendar lesson.