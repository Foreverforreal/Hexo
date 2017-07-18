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
<!-- more -->

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
    width: 20px;
}
table th:nth-of-type(10) {
    width:250px;
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
***
> 本节讨论定义一周中的日期的枚举（DayWeek）和定义了月的枚举（Month）。

Date-Time API提供了指定一周中的日期和一年中的月份的枚举。
### DayWeek
DayOfWeek枚举包含七个常数，用于描述星期几：从MONDAY到SUNDAY。DayOfWeek常数的整数值范围从1（Monday）到7（Sunday）。使用定义的常量（DayOfWeek.FRIDAY）使您的代码更易读。

这个枚举还提供一些方法，类似基于时间类提供的方法。比如，下面的代码向“Monday”添加3天，并且打印结果。输出是“THURSDAY”：
```java
System.out.printf("%s%n", DayOfWeek.MONDAY.plus(3));
```
通过使用*getDisplayName(TextStyle, Locale) *方法，你可以以用户区域来检索标识一周中星期几的字符串。TextStyle枚举使您能够指定要显示的字符串类型：FULL,NARROW（通常是单个字母），或者SHORT（一个缩写）。STANDALONE TextStyle常量在某些语言中使用，其输出在用作日期的一部分时不同于自身使用时的输出。以下示例打印“Monday”的TextStyle的三个主要形式：
```java
DayOfWeek dow = DayOfWeek.MONDAY;
Locale locale = Locale.getDefault();
System.out.println(dow.getDisplayName(TextStyle.FULL, locale));
System.out.println(dow.getDisplayName(TextStyle.NARROW, locale));
System.out.println(dow.getDisplayName(TextStyle.SHORT, locale));
```
此代码对于en语言环境的输出为：
```
Monday
M
Mon
```
### Month
Month枚举包括十二个月份的常量，从JANUARY到DECEMBER。与DayOfWeek枚举一样，Month枚举是强类型，每个常数的整数值对应于从1（January）到12（December）的ISO范围。使用定义的常量（Month.SEPTEMBER）使您的代码更易读。

Month枚举同样包含一些方法。以下代码行使用maxLength方法打印2月份的最大可能天数。输出是“29”：
```java
System.out.printf("%d%n", Month.FEBRUARY.maxLength());
```
Month枚举同样实现了 getDisplayName(TextStyle, Locale) 方法来获取一个以使用指定的TextStyle标识用户区域设置的月份的字符串。如果未定义特定的TextStyle，则返回表示常量数值的字符串。以下代码使用三种主要文本样式打印8月份：
```java
Month month = Month.AUGUST;
Locale locale = Locale.getDefault();
System.out.println(month.getDisplayName(TextStyle.FULL, locale));
System.out.println(month.getDisplayName(TextStyle.NARROW, locale));
System.out.println(month.getDisplayName(TextStyle.SHORT, locale));
```
此代码对于en语言环境的输出为：
```
August
A
Aug
```
## Date类
***
> 本节显示只处理日期的基于时间类，不包括时间和时区。这四个类是Localdate，YearMonthMonthDay和Year。

Date-Time API提供四个专门处理日期信息的类，而不考虑时间或时区。这些类由类名称来建议使用：LocalDate，YearMonth，MonthDay和Year。

### LocalDate
LocalDate表示ISO日历中的一个年-月-日，对于无时间表示日期很有用。您可以使用LocalDate来跟踪重大事件，例如出生日期或婚礼日期。以下示例使用of和with方法创建LocalDate的实例：
```java
LocalDate date = LocalDate.of(2000, Month.NOVEMBER, 20);
LocalDate nextWed = date.with(TemporalAdjusters.next(DayOfWeek.WEDNESDAY));
```
有关TemporalAdjuster接口的更多信息，请参阅[时间调整器]()。  

除了常用的方法之外，LocalDate类还提供了获取有关给定日期信息的getter方法。getDayOfWeek方法返回特定日期所在的星期几。例如，以下代码行返回“MONDAY”：
```java
DayOfWeek dotw = LocalDate.of(2012, Month.JULY, 9).getDayOfWeek();
```
以下示例使用TemporalAdjuster来获取在特定日期之后的第一个星期三。
```java
LocalDate date = LocalDate.of(2000, Month.NOVEMBER, 20);
TemporalAdjuster adj = TemporalAdjusters.next(DayOfWeek.WEDNESDAY);
LocalDate nextWed = date.with(adj);
System.out.printf("For the date of %s, the next Wednesday is %s.%n",
                  date, nextWed);
 ```
运行代码产生以下内容：
```
For the date of 2000-11-20, the next Wednesday is 2000-11-22.
```
“Period and Duration”部分还有使用LocalDate类的示例。
### YearMonth
YearMonth类代表特定年份的月份。以下示例使用YearMonth.lengthOfMonth()方法来确定多个年份月份组合的拥有的天数。
```java
YearMonth date = YearMonth.now();
System.out.printf("%s: %d%n", date, date.lengthOfMonth());

YearMonth date2 = YearMonth.of(2010, Month.FEBRUARY);
System.out.printf("%s: %d%n", date2, date2.lengthOfMonth());

YearMonth date3 = YearMonth.of(2012, Month.FEBRUARY);
System.out.printf("%s: %d%n", date3, date3.lengthOfMonth());
```
此代码的输出如下所示：
```
2013-06: 30
2010-02: 28
2012-02: 29
```
### MonthDay
MonthDay代表特定月份的天数，比如元旦在1月1日。

以下示例使用MonthDay.isValidYear方法来确定2月29日是否对2010年有效。调用返回false，确认2010年不是闰年。
```java
MonthDay date = MonthDay.of(Month.FEBRUARY, 29);
boolean validLeapYear = date.isValidYear(2010);
```
### Year
Year类表示一个年数。以下示例使用Year.isLeap方法来确定给定年份是否为闰年。调用返回true，确认2012年是闰年。
```java
boolean validLeapYear = Year.of(2012).isLeap();
```
## Date和Time 类
***
>本节介绍分别处理时间，日期和时间，但没有时区的LocalTime和LocalDateTime类。

### LocalTime
LocalTime类与名称前缀为Local的其他类相似，但是仅用于处理时间。这个类可以用于表示基于人的时间，例如电影时间，或本地图书馆的开放和关闭时间。它也可以用于创建数字时钟，如以下示例所示：
```java
LocalTime thisSec;

for (;;) {
    thisSec = LocalTime.now();

    // display代码的实现留给读者
    display(thisSec.getHour(), thisSec.getMinute(), thisSec.getSecond());
}
```
LocalTime类不存储时区或夏令时时间信息。

### LocalDateTime
用于同时处理日期和时间，而不处理时区的类是LocalDateTime,它是Date-Time API的一个核心类。该类用于表示日期（月 - 日 - 年）和时间（小时 - 分钟 - 秒 - 毫秒），实际上是LocalDate与LocalTime的组合。这个类可以用来代表一个特定的事件，比如在美国杯挑战者系列赛的路易威登杯决赛的第一场比赛，比赛开始于下午1:10在 2013年8月17日。请注意，这意味着下午1点10分在当地时间。要包括时区，您必须使用ZonedDateTime或OffsetDateTime，如Time Zone和Offset类中所述。

除了每个基于时间类都提供的now方法之外，LocalDateTime类都有各种创建LocalDateTime实例的of方法（或者前缀为of方法）。还有一个from方法，它将一个实例从另一个时间格式转换为LocalDateTime实例。还有一些方法可以用来加减小时，分中，天，周，月数。以下示例显示了这些方法中的一些。日期时间表达式为粗体：
```java
System.out.printf("now: %s%n", LocalDateTime.now());

System.out.printf("Apr 15, 1994 @ 11:30am: %s%n",
                  LocalDateTime.of(1994, Month.APRIL, 15, 11, 30));

System.out.printf("now (from Instant): %s%n",
                  LocalDateTime.ofInstant(Instant.now(), ZoneId.systemDefault()));

System.out.printf("6 months from now: %s%n",
                  LocalDateTime.now().plusMonths(6));

System.out.printf("6 months ago: %s%n",
                  LocalDateTime.now().minusMonths(6));
```
此代码生成的输出将类似于以下内容：
```
now: 2013-07-24T17:13:59.985
Apr 15, 1994 @ 11:30am: 1994-04-15T11:30
now (from Instant): 2013-07-24T17:14:00.479
6 months from now: 2014-01-24T17:14:00.480
6 months ago: 2013-01-24T17:14:00.481
```
## Time Zone和Offset类
***
> 本节讨论存储时区（或时区偏移）信息的基于时间类，ZonedDateTime，OffsetDateTime和OffsetTime。还讨论了一些支持类，ZoneId，ZoneRules和ZoneOffset。

时区是使用了相同标准时间的地球的一个区域。每个时区由一个标识符来描述，通常有区域/城市（Asia/Tokyo）和与Greenwich/UTC 时间偏移这样的格式。例如，东京的偏移是+09：00。
### ZoneId和ZoneOffset
Date-Time API提供了两个指定时区或偏移量的类：
- **ZoneId**指定时区标识符，并提供在Instant和LocalDateTime之间转换的规则。
- **ZoneOffset**指定与Greenwich/UTC时间偏移的时区。

与Greenwich/UTC时间的偏移通常以整个小时定义，但是也有例外。下面TimeZoneId实例代码打印所有不以整小时定义的与Greenwich/UTC时间偏移的时区列表。
```java
Set<String> allZones = ZoneId.getAvailableZoneIds();
LocalDateTime dt = LocalDateTime.now();

// 使用这个时区set创建一个List，并且将其排序
List<String> zoneList = new ArrayList<String>(allZones);
Collections.sort(zoneList);

...

for (String s : zoneList) {
    ZoneId zone = ZoneId.of(s);
    ZonedDateTime zdt = dt.atZone(zone);
    ZoneOffset offset = zdt.getOffset();
    int secondsOfHour = offset.getTotalSeconds() % (60 * 60);
    String out = String.format("%35s %10s%n", zone, offset);

    //将不是整小时偏移的时区写入到标准输出
    if (secondsOfHour != 0) {
        System.out.printf(out);
    }
    ...
}
```
此示例将以下列表打印到标准输出：
```
  America/Caracas     -04:30
     America/St_Johns     -02:30
        Asia/Calcutta     +05:30
         Asia/Colombo     +05:30
           Asia/Kabul     +04:30
       Asia/Kathmandu     +05:45
        Asia/Katmandu     +05:45
         Asia/Kolkata     +05:30
         Asia/Rangoon     +06:30
          Asia/Tehran     +04:30
   Australia/Adelaide     +09:30
Australia/Broken_Hill     +09:30
     Australia/Darwin     +09:30
      Australia/Eucla     +08:45
        Australia/LHI     +10:30
  Australia/Lord_Howe     +10:30
      Australia/North     +09:30
      Australia/South     +09:30
 Australia/Yancowinna     +09:30
  Canada/Newfoundland     -02:30
         Indian/Cocos     +06:30
                 Iran     +04:30
              NZ-CHAT     +12:45
      Pacific/Chatham     +12:45
    Pacific/Marquesas     -09:30
      Pacific/Norfolk     +11:30
```
TimeZoneId示例还会将所有时区ID的列表打印到名为timeZones的文件中。
### Date-Time类
Date-Time API提供了三个使用时区的基于时间的类：
- **ZonedDateTime**以相应的时区以及与Greenwich/UTC的时区偏移来处理日期和时间。
- **OffsetDateTime **以相应的与Greenwich/UTC时区偏移来处理日期和时间，但是没有时区ID。
- **OffsetTime **以相应的与Greenwich/UTC时区偏移来处理时间，但是没有时区ID。

什么时候使用OffsetDateTime而不是ZonedDateTime？如果你正在编写一个复杂的软件，它基于地理位置来模拟它自己的日期和时间计算规则，或者你要将时间戳存储到一个数据中，值追踪与Greenwich/UTC时间的绝对偏移，那么你可能想要使用OffsetDatetime.此外，XML和其他网络格式将日期-时间传输定义为OffsetDateTime或OffsetTime。

虽然所有三类都维持一个与Greenwich/UTC 时间的偏移，但是只有ZonedDateTime使用ZoneRules，它是java.time.zone包的一部分，用来决定确定偏移对于特定时区如何变化。例如，当将时钟向前移动到夏令时间时，大多数时区经历一个间隙（通常为1小时），以及一个时间重叠，当将时钟调回标准时间，重复转换前的最后一小时。ZonedDateTime类适应这种情况，而没有访问ZoneRules的OffsetDateTime和OffsetTime类则没有。

#### ZonedDateTime
实际上，ZonedDateTime类是将LocalDateTime类与ZoneId类组合。它用于表示具有时区（区域/城市，如 Europe/Paris）的完整日期（年，月，日）和时间（小时，分，秒，毫秒）。

 Flight示例中的以下代码定义了一个从洛杉矶到东京的航班起飞时间为ZonedDateTime，在 America/Los Angeles时区。withZoneSameInstant和plusMinutes方法用于创建一个ZonedDateTime的实例，它表示在650分钟飞行后在东京的预计到达时间。ZoneRules.isDaylightSavings方法决定飞机在东京到达时是否是夏令时。
 
 DateTimeFormatter对象用于格式化ZonedDateTime实例进行打印：
```java
DateTimeFormatter format = DateTimeFormatter.ofPattern("MMM d yyyy  hh:mm a");

// 于2013年7月20日下午7:30离开旧金山。
LocalDateTime leaving = LocalDateTime.of(2013, Month.JULY, 20, 19, 30);
ZoneId leavingZone = ZoneId.of("America/Los_Angeles"); 
ZonedDateTime departure = ZonedDateTime.of(leaving, leavingZone);

try {
    String out1 = departure.format(format);
    System.out.printf("LEAVING:  %s (%s)%n", out1, leavingZone);
} catch (DateTimeException exc) {
    System.out.printf("%s can't be formatted!%n", departure);
    throw exc;
}

//飞行时间为10小时50分钟，或650分钟
ZoneId arrivingZone = ZoneId.of("Asia/Tokyo"); 
ZonedDateTime arrival = departure.withZoneSameInstant(arrivingZone)
                                 .plusMinutes(650);

try {
    String out2 = arrival.format(format);
    System.out.printf("ARRIVING: %s (%s)%n", out2, arrivingZone);
} catch (DateTimeException exc) {
    System.out.printf("%s can't be formatted!%n", arrival);
    throw exc;
}

if (arrivingZone.getRules().isDaylightSavings(arrival.toInstant())) 
    System.out.printf("  (%s daylight saving time will be in effect.)%n",
                      arrivingZone);
else
    System.out.printf("  (%s standard time will be in effect.)%n",
                      arrivingZone);
```
这产生以下输出：
```
LEAVING:  Jul 20 2013  07:30 PM (America/Los_Angeles)
ARRIVING: Jul 21 2013  10:20 PM (Asia/Tokyo)
  (Asia/Tokyo standard time will be in effect.)
```
#### OffsetDateTime
OffsetDateTime类实际上是将LocalDateTime类与ZoneOffset类组合在一起。它用于表示具有与Greenwich/UTC时间偏移（+/-小时:分钟，如+06:00或 - 08:00）的完整日期（年，月，日）和时间（小时，分，秒，毫秒）。

以下示例使用TemporalAdjuster.lastDay方法的OffsetDateTime来查找2013年7月的最后一个星期四。
```java
// 查找2013年7月的最后一个星期四。
LocalDateTime localDate = LocalDateTime.of(2013, Month.JULY, 20, 19, 30);
ZoneOffset offset = ZoneOffset.of("-08:00");

OffsetDateTime offsetDate = OffsetDateTime.of(localDate, offset);
OffsetDateTime lastThursday =
        offsetDate.with(TemporalAdjusters.lastInMonth(DayOfWeek.THURSDAY));
System.out.printf("The last Thursday in July 2013 is the %sth.%n",
                   lastThursday.getDayOfMonth());
```
运行代码的输出为
```
The last Thursday in July 2013 is the 25th.
```
#### OffsetTime
OffsetTime类实际上是将LocalTime类与ZoneOffset类组合在一起。它用于表示具有与Greenwich/UTC时间偏移（+/-小时:分钟，如+06:00或 - 08:00）的时间（小时，分，秒，毫秒）。

OffsetTime类在与OffsetDateTime类相同的情况下使用，但不需要跟踪日期时。
## Instant类
***
> 本节讨论了Instant类，它代表了时间轴上的瞬间时刻。

Date-Time API的核心类之一是Instant类，它表示在时间轴上的毫秒的开始。此类可用于生成时间戳来表示机器时间。
```java
import java.time.Instant;

Instant timestamp = Instant.now();
```
从Instant类返回的值是从1970年1月1日（1970-01-01T00：00：00Z）的第一秒开始的计时，这个时间点也被称为EPOCH。发生在在EPOCH之前的instant有一个负值，发生在EPOCH之后instant有一个正值。

其他Instant类提供的常数是MIN，表示最小可能（最近的）时间点，MAX代表最大（最远的）的时间点。

在Instant上调用toString会产生如下所示的输出：
```
2013-05-30T23:38:23.085Z
```
此格式遵循ISO-8601标准，用于表示日期和时间。

Instant类提供了各种操纵Instant的方法。它有plus和minus方法来加减时间。以下代码为当前时间添加1小时：
```java
Instant oneHourLater = Instant.now().plusHours(1);
```
还有方法用来比较instant，比如isAfter和isBefore。until方法返回两个Instant对象之间存在多少时间。以下代码行报告自Java EPOCH开始以来发生了多少秒。
```java
long secondsFromEpoch = Instant.ofEpochSecond(0L).until(Instant.now(),
                        ChronoUnit.SECONDS);
```
Instant类不适用于人类的时间单位，比如年，月或日。如果要在这些单位中执行计算，您可以通过将Instant绑定到时区，将Instant转换为另一个类，例如LocalDateTime或ZonedDateTime。然后你可以使用期望的单位访问它的值。下面代码使用ofInstant方法和默认时区将一个Instant转换为LocalDateTime对象，并且以更易读形式打印输出日期时间：
```java
Instant timestamp;
...
LocalDateTime ldt = LocalDateTime.ofInstant(timestamp, ZoneId.systemDefault());
System.out.printf("%s %d %d at %d:%d%n", ldt.getMonth(), ldt.getDayOfMonth(),
                  ldt.getYear(), ldt.getHour(), ldt.getMinute());
```
输出将类似于以下内容：
```
MAY 30 2013 at 18:21
```
ZonedDateTime或OffsetTimeZone对象都可以转换为Instant对象，每个都映射到时间轴上的一个精确时刻。然而，反之则不然。要将Instant对象转换为ZonedDateTime或OffsetDateTime对象，需要提供时区或时区偏移信息。

## 解析和格式化
***
> 本节提供了如何使用预定义的formatter来格式和解析日期和时间值的概述。

Date-Time API中的基于时间的类提供了用于解析包含日期和时间信息的字符串的parse方法。这些类还提供了format方法用于格式化显示基于时间的对象。在这两个情景中，处理是类似的：你提供一个模式给DateTimeFormatter来创建一个formatter对象。然后这个formatter被传递给parse和format方法。

DateTimeFormatter类提供了许多预定义的formatter，也可以定义自己的formatter。

如果在转换过程中出现问题，则parse和format方法会抛出异常。因此，您的解析代码应该捕获DateTimeParseException错误，您的格式化代码应该捕获DateTimeException错误。

DateTimeFormatter类是不可变的和线程安全的;它可以（并且应该）适当地分配给静态常量。
> 版本注意:java.time中的date-time对象可以通过使用的基于模式的格式化形式，直接与java.util.Formatter和String.format一起使用，这是旧的java.util.Date和java.util.Calendar类所使用的方式。。

### 解析
LocalDate类中的单参数 parse(CharSequence)方法使用ISO\_LOCAL\_DATE formatter。要指定不同的formatter，你可以使用双参数方法parse(CharSequence, DateTimeFormatter)。下面的例子使用预定义的BASIC\_ISO\_DATE formatter，它将为July 9, 1959使用19590709格式。
```java
String in = ...;
LocalDate date = LocalDate.parse(in, DateTimeFormatter.BASIC_ISO_DATE);
```
您还可以使用自己的模式定义一个格式化程序。 Parse示例中的以下代码创建一个formatter，该formatter应用格式为“MMM d yyyy”。此格式指定三个字符以表示月份，一个数字表示月份的日期，四位数字表示年份。使用此模式创建的formatter将识别字符串，例如“2003年1月3日”或“1993年3月23日”。然而，要将格式指定为“MMM dd yyyy”，用两个字符表示月份的天数，那么您必须始终使用两个字符，对于一位数字的日期会填充一个零：“Jun 03 2003”。
```java
String input = ...;
try {
    DateTimeFormatter formatter =
                      DateTimeFormatter.ofPattern("MMM d yyyy");
    LocalDate date = LocalDate.parse(input, formatter);
    System.out.printf("%s%n", date);
}
catch (DateTimeParseException exc) {
    System.out.printf("%s is not parsable!%n", input);
    throw exc;      // 重抛出异常
}
//'date'已成功解析
```
DateTimeFormatter类的文档指定了可用于指定格式化或解析模式的[完整符号列表](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html#patterns)。

### 格式化
format(DateTimeFormatter)方法使用指定的格式将一个基于时间对象转换为一个字符串代表形式。下面Flight示例的代码中，使用 "MMM d yyy hh:mm a"格式转化了一个ZonedDateTime实例。日期的定义方式与上一个解析示例中使用的方式相同，但此模式还包括小时，分钟，以及a.m.和p.m.组件。
```java
ZoneId leavingZone = ...;
ZonedDateTime departure = ...;

try {
    DateTimeFormatter format = DateTimeFormatter.ofPattern("MMM d yyyy  hh:mm a");
    String out = departure.format(format);
    System.out.printf("LEAVING:  %s (%s)%n", out, leavingZone);
}
catch (DateTimeException exc) {
    System.out.printf("%s can't be formatted!%n", departure);
    throw exc;
}
```
此示例的输出打印到达和离开时间如下：
```
LEAVING:  Jul 20 2013  07:30 PM (America/Los_Angeles)
ARRIVING: Jul 21 2013  10:20 PM (Asia/Tokyo)
```


## Temporal包
***
> 本节介绍了java.time.temporal包的概述，它支持时间类，字段（TemporalField和ChronoField）和单位（TemporalUnit和ChronoUnit）本节还解释了如何使用时间调整器来检索调整的时间值，例如“4月11日之后的第一个星期二”以及如何执行时间查询。

java.time.temporal包提供了一组接口，类和枚举，支持日期和时间代码，特别是日期和时间计算。

这些接口旨在用于最低层次。通常应用程序代码应根据具体类型声明变量和参数，如LocalDate或ZonedDateTime，而不是Temporal接口。这与声明一个String类型，而不是CharSequence类型的变量完全一样。

### 包接口
#### Temporal和 TemporalAccessor
Temporal接口提供了一个用于访问基于时间对象的框架，并且它被基于时间的类如Instant，LocalDateTime和ZonedDateTime等所实现。这个接口提供了用于加减时间单位的方法，使得基于时间的算法在各种日期和时间类中更容易和一致。TemporalAccessor提供了一个只读版本的Temporal接口。

Temporal和TemporalAccessor对象按照TemporalField接口中指定的字段进行定义。ChronoField枚举是TemporalField接口的具体实现，并提供了一组丰富的定义常量，如DAY\_OF\_WEEK，MINUTE\_OF\_HOUR和MONTH\_OF\_YEAR。

这些字段的单位由TemporalUnit接口指定。 ChronoUnit枚举实现了TemporalUnit接口。 ChronoField.DAY\_OF\_WEEK字段是ChronoUnit.DAYS和ChronoUnit.WEEKS的组合。ChronoField和ChronoUnit枚举将在以下部分讨论。

Temporal接口中基于算术的方法需要使用按照TemporalAmount值定义的参数。Period和Duration类（在Period和Duration中讨论）实现TemporalAmount界面。

#### ChronoField和IsoFields
ChronoField枚举实现了TemporalField 接口，它提供了一组丰富的常量用来访问日期和时间值。几个例子是CLOCK\_HOUR\_OF\_DAY，NANO\_OF\_DAY和DAY\_OF\_YEAR。这个枚举可以用来表达时间的概念方面，比如一年的第三周，一天的第十一个小时，或者这个月的第一个星期一。当你遇到一个未知类型的Temporal 时，你可以使用TemporalAccessor.isSupported(TemporalField)方法来确定这个Temporal是否支持特定字段。下面一行代码返回一个false，表示这个LocalDate不支持ChronoField.CLOCK\_HOUR\_OF\_DAY:
```java
boolean isSupported = LocalDate.now().isSupported(ChronoField.CLOCK_HOUR_OF_DAY);
```
特定于ISO-8601日历系统的其他字段在IsoFields类中定义。以下示例显示如何使用ChronoField和IsoFields获取字段的值：
```java
time.get(ChronoField.MILLI_OF_SECOND)
int qoy = date.get(IsoFields.QUARTER_OF_YEAR);
```
另外两个类定义了可能有用的附加字段，WeekFields和JulianFields。

#### ChronoUnit
ChronoUnit枚举实现了TemporalUnit接口，并提供了一组基于日期和时间的标准单位，从毫秒到千年。请注意，并不是所有类都支持所有ChronoUnit对象。例如，Instant类不支持ChronoUnit.MONTHS或ChronoUnit.YEARS。TemporalAccessor.isSupported（TemporalUnit）方法可用于验证一个类是否支持特定的时间单位。对isSupported的以下调用返回false，确认Instant类不支持ChronoUnit.DAYS。
```java
boolean isSupported = instant.isSupported(ChronoUnit.DAYS);
```
### Temporal Adjuster
在java.time.temporal包中的TemporalAdjuster接口提供了接收一个Temporal值，并且返回一个调整值。这个调节器可以与任何基于时间的类型一起使用。

如果调节器与ZonedDateTime一起使用，则会计算新的日期，保留原始时间和时区值。
#### 预定义调节器
TemporalAdjusters类（注意复数）提供一组预定义的调节器，用于查找本月的第一天或最后一天，一年中的最后一天，每月的最后一个星期三，或特定日期之后的第一个星期二，举几个例子。预定义的调整器被定义为静态方法，并被设计为与静态导入语句一起使用。

下面的示例使用了几个TemporalAdjusters方法，并且与基于时间类中定义的with方法结合使用，根据2000年10月15日这个原始日期来计算新的日期：
```java
LocalDate date = LocalDate.of(2000, Month.OCTOBER, 15);
DayOfWeek dotw = date.getDayOfWeek();
System.out.printf("%s is on a %s%n", date, dotw);

System.out.printf("first day of Month: %s%n",
                  date.with(TemporalAdjusters.firstDayOfMonth()));
System.out.printf("first Monday of Month: %s%n",
                  date.with(TemporalAdjusters.firstInMonth(DayOfWeek.MONDAY)));
System.out.printf("last day of Month: %s%n",
                  date.with(TemporalAdjusters.lastDayOfMonth()));
System.out.printf("first day of next Month: %s%n",
                  date.with(TemporalAdjusters.firstDayOfNextMonth()));
System.out.printf("first day of next Year: %s%n",
                  date.with(TemporalAdjusters.firstDayOfNextYear()));
System.out.printf("first day of Year: %s%n",
                  date.with(TemporalAdjusters.firstDayOfYear()));
```
这产生以下输出
```
2000-10-15 is on a SUNDAY
first day of Month: 2000-10-01
first Monday of Month: 2000-10-02
last day of Month: 2000-10-31
first day of next Month: 2000-11-01
first day of next Year: 2001-01-01
first day of Year: 2000-01-01
```
#### 自定义调节器
你也可以创建自己的自定义调节器。为此，你要创建一个实现了TemporalAdjuster接口和它的adjustInto(Temporal)方法的类。NextPayday示例中的PaydayAdjuster类是一个自定义调节器。PaydayAdjuster评估传入日期并返回下一个发薪日，假设发薪日每月发生两次：第一次在第15日，再次在当月的最后一天。如果计算的日期发生在周末，则使用上一个星期五。假定当前的日历年。
```java
/**
 * adjustInto方法接收一个Temporal实例
 * 并且返回一个调节后的LocalDate如果传入
 *的参数不是一个LocalDate，那么抛出一个DateTimeException 
 */
public Temporal adjustInto(Temporal input) {
    LocalDate date = LocalDate.from(input);
    int day;
    if (date.getDayOfMonth() < 15) {
        day = 15;
    } else {
        day = date.with(TemporalAdjusters.lastDayOfMonth()).getDayOfMonth();
    }
    date = date.withDayOfMonth(day);
    if (date.getDayOfWeek() == DayOfWeek.SATURDAY ||
        date.getDayOfWeek() == DayOfWeek.SUNDAY) {
        date = date.with(TemporalAdjusters.previous(DayOfWeek.FRIDAY));
    }

    return input.with(date);
}
```
这个调节器和预定义的调节器有相同的调用方式，与with方法一起使用。下面的一行代码来自NextPayday示例：
```java
LocalDate nextPayday = date.with(new PaydayAdjuster());
```
在2013年，6月15和6月30都在周末。运行NextPayday示例，分别为6月3日和6月18日（2013年），具有以下结果：
```java
Given the date:  2013 Jun 3
the next payday: 2013 Jun 14

Given the date:  2013 Jun 18
the next payday: 2013 Jun 28
```
### Temporal Query
TemporalQuery可以用于从基于时间的对象中检索信息。
#### 预定义查询
TemporalQueries类（注意复数）提供了几个预定义的查询，包括当应用程序无法识别基于时间的对象的类型时有用的方法。与调节器一样，预定义的查询被定义为静态方法，并被设计为与静态导入语句一起使用。

例如，precision查询返回由特定的基于时间的对象最小的ChronoUnit。以下示例对几种类型的基于时间的对象使用precision查询：
```java
TemporalQueries query = TemporalQueries.precision();
System.out.printf("LocalDate precision is %s%n",
                  LocalDate.now().query(query));
System.out.printf("LocalDateTime precision is %s%n",
                  LocalDateTime.now().query(query));
System.out.printf("Year precision is %s%n",
                  Year.now().query(query));
System.out.printf("YearMonth precision is %s%n",
                  YearMonth.now().query(query));
System.out.printf("Instant precision is %s%n",
                  Instant.now().query(query)); 
```
输出如下所示：
```
LocalDate precision is Days
LocalDateTime precision is Nanos
Year precision is Years
YearMonth precision is Months
Instant precision is Nanos
```
#### 自定义查询
你也可以创建自己的自定义查询。这样做的一个方式是创建一个实现了TemporalQuery 接口和它的queryFrom(TemporalAccessor) 方法的类。CheckDate示例实现两个自定义查询。第一个自定义查询可以在FamilyVacations类中找到，它实现了TemporalQuery接口。queryFrom方法将传入的日期与预订假期进行比较，如果属于这些日期范围，则返回TRUE。
```java
// 如果传入日期发生在家庭假期之一则返回true
// 因为查询只比较月和天，即使Temporal类型不一致
//比较结果依旧认为相同
public Boolean queryFrom(TemporalAccessor date) {
    int month = date.get(ChronoField.MONTH_OF_YEAR);
    int day   = date.get(ChronoField.DAY_OF_MONTH);

    // 迪士尼乐园过春节
    if ((month == Month.APRIL.getValue()) && ((day >= 3) && (day <= 8)))
        return Boolean.TRUE;

    //史密斯家族团聚在Saugatuck湖上
    if ((month == Month.AUGUST.getValue()) && ((day >= 8) && (day <= 14)))
        return Boolean.TRUE;

    return Boolean.FALSE;
}
```
第二个自定义查询实现在FamilyBirthdays类中。该类提供了一个isFamilyBirthday方法，将传入日期与几个生日进行比较，如果有匹配，则返回TRUE。
```java
// 如果传入的日期和家庭生日中的一个一样则返回true
// 因为查询只比较月和天，即使Temporal类型不一致
//比较结果依旧认为相同
public static Boolean isFamilyBirthday(TemporalAccessor date) {
    int month = date.get(ChronoField.MONTH_OF_YEAR);
    int day   = date.get(ChronoField.DAY_OF_MONTH);

    // 安吉的生日是4月3日。
    if ((month == Month.APRIL.getValue()) && (day == 3))
        return Boolean.TRUE;

    // 苏的生日是6月18日。
    if ((month == Month.JUNE.getValue()) && (day == 18))
        return Boolean.TRUE;

    // 乔的生日是5月29日。
    if ((month == Month.MAY.getValue()) && (day == 29))
        return Boolean.TRUE;

    return Boolean.FALSE;
}
```
FamilyBirthday类没有实现TemporalQuery接口并且可以用作lambda表达式的一部分。下面CheckDate中的代码显示了如何调用这两个自定义查询。
```java
// 不使用lambda表达式调用查询
Boolean isFamilyVacation = date.query(new FamilyVacations());

// 使用lambda表达式调用查询
Boolean isFamilyBirthday = date.query(FamilyBirthdays::isFamilyBirthday);

if (isFamilyVacation.booleanValue() || isFamilyBirthday.booleanValue())
    System.out.printf("%s is an important date!%n", date);
else
    System.out.printf("%s is not an important date.%n", date);
```
## Period and Duration

This section explains how to calculate an amount of time, using both the Period and Duration classes, as well as the ChronoUnit.between method.

## Clock

This section provides a brief overview of the Clock class. You can use this class to provide an alternative clock to the system clock.

## Non-ISO Date Conversion

This section explains how to convert from a date in the ISO calendar system to a date in a non-ISO calendar system, such as a JapaneseDate or a ThaiBuddhistDate.

## Legacy Date-Time Code

This section offers some tips on how to convert older java.util.Date and java.util.Calendar code to the Date-Time API.

## Summary

This section provides a summary of the Standard Calendar lesson.