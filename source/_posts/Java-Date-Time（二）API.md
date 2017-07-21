title: Java Date-Time（二）API
id: 1500354446707
author: 不识
tags:
  - java
categories:
  - java 基础
  - java Date-Time
  - ''
date: 2017-07-18 13:07:00
---
Date-Time API的核心是java.time包。定义在java.time中的类的它们的日历系统是基于ISO日历，它是代表日期和时间的世界标准。ISO日历遵循前公历规则。公历日历在1582年引入；在前公历日历中，日期从那时起向后延伸，以创建一致，统一的时间线，并简化日期计算。
***
# 概述
***
>本节比较人类时间和机器时间的概念，提供了java.time包中主要的基于时间的类的表。
     
代表时间有两种基本方式。一种是以人为本代表时间的方式，称为人类时间，如年，月，日，时，分，秒。另一种方式是机器时间，以一个纳秒的分辨率，从一个起源的时间线不断地测量时间，被称为时代。Date-Time包提供了丰富的类来代表日期和时间。在Date-Time API中一些类旨在表示机器时间，而另一些则更适合代表人类时间。

首先确定您需要哪些方面的日期和时间，然后选择满足这些需求的类。当你选择基于时间的类后，你首先决定是否需要代表人类时间还是机器时间。然后，您将确定您需要代表时间的哪些方面。你是需要一个时区？日期和时间？只是日期？如果你需要一个日期，你是需要月，天，年，还是子集？
> 术语：在Date-Time API中捕获并使用日期或时间的值的类，比如Instant，LocalDateTime和ZonedDateTime，在本教程中被称为基于时间的类（或类型）。支持类型，如TemporalAdjuster接口或DayOfWeek枚举，不包括在此定义中。

比如，你可能使用一个LocalDate对象来表示生日日期，因为大多数人注意它们的生日，无论他们是在他们的出生城市还是跨过全球，在国际日期变更线的另一边。如果您正在追踪占星术时间，那么您可能希望使用LocalDateTime对象来表示出生日期和时间，或者ZonedDateTime，它还包括时区。如果你创建一个时间戳，那么你最可能想要使用一个Instant，它允许您将时间轴上的一个瞬时点与另一个瞬时点进行比较。

下面的表格总结了java.time包中的基于时间的类，它们存储了日期与/或时间的信息，或者可以用来衡量一段时间。在列中的对号指示该类使用该特定类型的数据，并且toString Output列显示使用toString方法打印的实例。Where Discussed列链接到本教程相应的位置。

<style>
table:first-of-type th:first-of-type {
    width: 98px;
}
table:first-of-type th:nth-of-type(2),table:first-of-type th:nth-of-type(3),table:first-of-type th:nth-of-type(4),table:first-of-type th:nth-of-type(5),table:first-of-type th:nth-of-type(6),table:first-of-type th:nth-of-type(7),table:first-of-type th:nth-of-type(8),table:first-of-type th:nth-of-type(9){
    width: 20px;
}
table:first-of-type th:nth-of-type(10) {
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
***
# DayWeek和Month枚举
***
> 本节讨论定义一周中的日期的枚举（DayWeek）和定义了月的枚举（Month）。

Date-Time API提供了指定一周中的日期和一年中的月份的枚举。
## DayWeek
***
DayOfWeek枚举包含七个常数，用于描述星期几：从MONDAY到SUNDAY。DayOfWeek常数的整数值范围从1（Monday）到7（Sunday）。使用定义的常量（DayOfWeek.FRIDAY）使您的代码更易读。

这个枚举还提供一些方法，类似基于时间类提供的方法。比如，下面的代码向“Monday”添加3天，并且打印结果。输出是“THURSDAY”：
```java
System.out.printf("%s%n", DayOfWeek.MONDAY.plus(3));
```
通过使用getDisplayName(TextStyle, Locale)方法，你可以以用户区域来检索标识一周中星期几的字符串。TextStyle枚举使您能够指定要显示的字符串类型：FULL,NARROW（通常是单个字母），或者SHORT（一个缩写）。STANDALONE TextStyle常量在某些语言中使用，其输出在用作日期的一部分时不同于自身使用时的输出。以下示例打印“Monday”的TextStyle的三个主要形式：
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
## Month
***
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
***
# Date类
***
> 本节显示只处理日期的基于时间类，不包括时间和时区。这四个类是Localdate，YearMonth，MonthDay和Year。

Date-Time API提供四个专门处理日期信息的类，而不考虑时间或时区。这些类由类名称来建议使用：LocalDate，YearMonth，MonthDay和Year。

## LocalDate
***
LocalDate表示ISO日历中的一个年-月-日，对于无时间表示日期很有用。您可以使用LocalDate来跟踪重大事件，例如出生日期或婚礼日期。

“本地”，这个术语，我们对它的熟悉来自于Joda-Time。它原本出自ISO-8061的时间和日期标准，它和时区无关。实际上，本地日期只是日期的描述，例如“2014年4月5日”。特定的本地时间，因你在地球上的不同位置，开始于不同的时间线。所以，澳大利亚的本地时间开始的比伦敦早10小时，比旧金山早18小时。

以下示例使用of和with方法创建LocalDate的实例：
```java
LocalDate date = LocalDate.of(2000, Month.NOVEMBER, 20);
LocalDate nextWed = date.with(TemporalAdjusters.next(DayOfWeek.WEDNESDAY));
```
有关TemporalAdjuster接口的更多信息，请参阅[时间修改器]()。  

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
## YearMonth
***
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
## MonthDay
***
MonthDay代表特定月份的天数，比如元旦在1月1日。

以下示例使用MonthDay.isValidYear方法来确定2月29日是否对2010年有效。调用返回false，确认2010年不是闰年。
```java
MonthDay date = MonthDay.of(Month.FEBRUARY, 29);
boolean validLeapYear = date.isValidYear(2010);
```
## Year
***
Year类表示一个年数。以下示例使用Year.isLeap方法来确定给定年份是否为闰年。调用返回true，确认2012年是闰年。
```java
boolean validLeapYear = Year.of(2012).isLeap();
```
***
# Date和Time 类
***
>本节介绍分别处理时间，日期和时间，但没有时区的LocalTime和LocalDateTime类。

## LocalTime
***
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

## LocalDateTime
***
用于同时处理日期和时间，而不处理时区的类是LocalDateTime,它是Date-Time API的一个核心类。该类用于表示日期（月 - 日 - 年）和时间（小时 - 分钟 - 秒 - 纳秒），实际上是LocalDate与LocalTime的组合。这个类可以用来代表一个特定的事件，比如在美国杯挑战者系列赛的路易威登杯决赛的第一场比赛，比赛开始于下午1:10在 2013年8月17日。请注意，这意味着下午1点10分在当地时间。要包括时区，您必须使用ZonedDateTime或OffsetDateTime，如Time Zone和Offset类中所述。

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
***
# Time Zone和Offset类
***
> 本节讨论存储时区（或时区偏移）信息的基于时间类，ZonedDateTime，OffsetDateTime和OffsetTime。还讨论了一些支持类，ZoneId，ZoneRules和ZoneOffset。

时区是使用了相同标准时间的地球的一个区域。每个时区由一个标识符来描述，通常有区域/城市（Asia/Tokyo）和与Greenwich/UTC 时间偏移这样的格式。例如，东京的偏移是+09：00。
## ZoneId和ZoneOffset
***
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
## Date-Time类
***
Date-Time API提供了三个使用时区的基于时间的类：
- **ZonedDateTime**以相应的时区以及与Greenwich/UTC的时区偏移来处理日期和时间。
- **OffsetDateTime **以相应的与Greenwich/UTC时区偏移来处理日期和时间，但是没有时区ID。
- **OffsetTime **以相应的与Greenwich/UTC时区偏移来处理时间，但是没有时区ID。

什么时候使用OffsetDateTime而不是ZonedDateTime？如果你正在编写一个复杂的软件，它基于地理位置来模拟它自己的日期和时间计算规则，或者你要将时间戳存储到一个数据中，值追踪与Greenwich/UTC时间的绝对偏移，那么你可能想要使用OffsetDatetime.此外，XML和其他网络格式将日期-时间传输定义为OffsetDateTime或OffsetTime。

虽然所有三类都维持一个与Greenwich/UTC 时间的偏移，但是只有ZonedDateTime使用ZoneRules，它是java.time.zone包的一部分，用来决定确定偏移对于特定时区如何变化。例如，当将时钟向前移动到夏令时间时，大多数时区经历一个间隙（通常为1小时），以及一个时间重叠，当将时钟调回标准时间，重复转换前的最后一小时。ZonedDateTime类适应这种情况，而没有访问ZoneRules的OffsetDateTime和OffsetTime类则没有。

### ZonedDateTime
实际上，ZonedDateTime类是将LocalDateTime类与ZoneId类组合。它用于表示具有时区（区域/城市，如 Europe/Paris）的完整日期（年，月，日）和时间（小时，分，秒，纳秒）。

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
### OffsetDateTime
OffsetDateTime类实际上是将LocalDateTime类与ZoneOffset类组合在一起。它用于表示具有与Greenwich/UTC时间偏移（+/-小时:分钟，如+06:00或 - 08:00）的完整日期（年，月，日）和时间（小时，分，秒，纳秒）。

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
### OffsetTime
OffsetTime类实际上是将LocalTime类与ZoneOffset类组合在一起。它用于表示具有与Greenwich/UTC时间偏移（+/-小时:分钟，如+06:00或 - 08:00）的时间（小时，分，秒，纳秒）。

OffsetTime类在与OffsetDateTime类相同的情况下使用，但不需要跟踪日期时。
***
# Instant类
***
> 本节讨论了Instant类，它代表了时间轴上的瞬间时刻。

Date-Time API的核心类之一是Instant类，它表示在时间轴上的纳秒的开始。此类可用于生成时间戳来表示机器时间。
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
***
# 解析和格式化
***
> 本节提供了如何使用预定义的formatter来格式和解析日期和时间值的概述。

java.time.format包是专门用来格式化输出时间/日期的。这个包围绕DateTimeFormatter类和它的辅助创建类DateTimeFormatterBuilder展开。

Date-Time API中的基于时间的类提供了用于解析包含日期和时间信息的字符串的parse方法。这些类还提供了format方法用于格式化显示基于时间的对象。在这两个情景中，处理是类似的：你提供一个模式给DateTimeFormatter来创建一个formatter对象。然后这个formatter被传递给parse和format方法。

DateTimeFormatter类提供了许多预定义的formatter，也可以定义自己的formatter。

如果在转换过程中出现问题，则parse和format方法会抛出异常。因此，您的解析代码应该捕获DateTimeParseException错误，您的格式化代码应该捕获DateTimeException错误。

静态方法加上DateTimeFormatter中的常量，是最通用的创建格式化器的方式。包括：

- 常用ISO格式常量，如ISO_LOCAL_DATE
- 字母模式，如ofPattern(“dd/MM/uuuu”)
- 本地化样式，如ofLocalizedDate(FormatStyle.MEDIUM)

DateTimeFormatter类是不可变的和线程安全的;它可以（并且应该）适当地分配给静态常量。

> 版本注意:java.time中的date-time对象可以通过使用的基于模式的格式化形式，直接与java.util.Formatter和String.format一起使用，这是旧的java.util.Date和java.util.Calendar类所使用的方式。。

## 解析
***
LocalDate类中的单参数 parse(CharSequence)方法使用ISO\_LOCAL\_DATE formatter。要指定不同的formatter，你可以使用双参数方法parse(CharSequence, DateTimeFormatter)。下面的例子使用预定义的BASIC\_ISO\_DATE formatter，它将为July 9, 1959使用19590709格式。
```java
String in = ...;
LocalDate date = LocalDate.parse(in, DateTimeFormatter.BASIC_ISO_DATE);
```
您还可以使用自己的模式定义一个fomatter。 Parse示例中的以下代码创建一个formatter，该formatter应用格式为“MMM d yyyy”。此格式指定三个字符以表示月份，一个数字表示月份的日期，四位数字表示年份。使用此模式创建的formatter将识别字符串，例如“2003年1月3日”或“1993年3月23日”。然而，要将格式指定为“MMM dd yyyy”，用两个字符表示月份的天数，那么您必须始终使用两个字符，对于一位数字的日期会填充一个零：“Jun 03 2003”。
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

## 格式化
***
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

***
# Temporal包
***
> 本节介绍了java.time.temporal包的概述，它支持时间类，字段（TemporalField和ChronoField）和单位（TemporalUnit和ChronoUnit）本节还解释了如何使用时间调整器来检索调整的时间值，例如“4月11日之后的第一个星期二”以及如何执行时间查询。

java.time.temporal包提供了一组接口，类和枚举，支持日期和时间代码，特别是日期和时间计算。

这些接口旨在用于最低层次。通常应用程序代码应根据具体类型声明变量和参数，如LocalDate或ZonedDateTime，而不是Temporal接口。这与声明一个String类型，而不是CharSequence类型的变量完全一样。

## 包接口
***
### Temporal和 TemporalAccessor
Temporal接口提供了一个用于访问基于时间对象的框架，并且它被基于时间的类如Instant，LocalDateTime和ZonedDateTime等所实现。这个接口提供了用于加减时间单位的方法，使得基于时间的算法在各种日期和时间类中更容易和一致。TemporalAccessor提供了一个只读版本的Temporal接口。

Temporal和TemporalAccessor对象按照TemporalField接口中指定的字段进行定义。ChronoField枚举是TemporalField接口的具体实现，并提供了一组丰富的定义常量，如DAY\_OF\_WEEK，MINUTE\_OF\_HOUR和MONTH\_OF\_YEAR。

这些字段的单位由TemporalUnit接口指定。 ChronoUnit枚举实现了TemporalUnit接口。 ChronoField.DAY\_OF\_WEEK字段是ChronoUnit.DAYS和ChronoUnit.WEEKS的组合。ChronoField和ChronoUnit枚举将在以下部分讨论。

Temporal接口中基于算术的方法需要使用按照TemporalAmount值定义的参数。Period和Duration类（在Period和Duration中讨论）实现TemporalAmount界面。

### ChronoField和IsoFields
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

### ChronoUnit
ChronoUnit枚举实现了TemporalUnit接口，并提供了一组基于日期和时间的标准单位，从毫秒到千年。请注意，并不是所有类都支持所有ChronoUnit对象。例如，Instant类不支持ChronoUnit.MONTHS或ChronoUnit.YEARS。TemporalAccessor.isSupported（TemporalUnit）方法可用于验证一个类是否支持特定的时间单位。对isSupported的以下调用返回false，确认Instant类不支持ChronoUnit.DAYS。
```java
boolean isSupported = instant.isSupported(ChronoUnit.DAYS);
```
## Temporal Adjuster
***
在java.time.temporal包中的TemporalAdjuster接口提供了接收一个Temporal值，并且返回一个修改值。这个修改器可以与任何基于时间的类型一起使用。

如果修改器与ZonedDateTime一起使用，则会计算新的日期，保留原始时间和时区值。
### 预定义修改器
TemporalAdjusters类（注意复数）提供一组预定义的修改器，用于查找本月的第一天或最后一天，一年中的最后一天，每月的最后一个星期三，或特定日期之后的第一个星期二，举几个例子。预定义的调整器被定义为静态方法，并被设计为与静态导入语句一起使用。

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
### 自定义修改器
你也可以创建自己的自定义修改器。为此，你要创建一个实现了TemporalAdjuster接口和它的adjustInto(Temporal)方法的类。NextPayday示例中的PaydayAdjuster类是一个自定义修改器。PaydayAdjuster评估传入日期并返回下一个发薪日，假设发薪日每月发生两次：第一次在第15日，再次在当月的最后一天。如果计算的日期发生在周末，则使用上一个星期五。假定当前的日历年。
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
这个修改器和预定义的修改器有相同的调用方式，与with方法一起使用。下面的一行代码来自NextPayday示例：
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
## Temporal Query
***
TemporalQuery可以用于从基于时间的对象中检索信息。
### 预定义查询
TemporalQueries类（注意复数）提供了几个预定义的查询，包括当应用程序无法识别基于时间的对象的类型时有用的方法。与修改器一样，预定义的查询被定义为静态方法，并被设计为与静态导入语句一起使用。

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
### 自定义查询
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
***
# Period和Duration
***
> 本节介绍如何使用Period和Duration类以及ChronoUnit.between方法来计算一段时间。

当您编写代码以指定一段时间时，请使用最符合您需求的类或方法：Duration类，Period类，或者ChronoUnit.between方法。Duration使用基于时间的值（秒，纳秒）测量一段时间。而Period使用基于日期的值（年，月，日）。
> 注意：表示一天的Duration是精确的24小时。而表示一天的Period添加到ZonedDateTime时，可能会更具时区而有所不同。例如，如果发生在夏令时的第一天或最后一天。

## Duration
***
Duration 最适合于测量基于机器的时间的情况，例如使用Instant对象的代码。一个Duration对象以秒或纳秒为单位测量时间，而不是使用基于日期的结构，比如年，月，日，尽管这个类提供了转换为天，小时和分钟的方法。如果一个Duration终点创建在起点之前，那么它可以有一个负值。

下面代码以纳秒计算两个时间点之间的持续时间：
```java
Instant t1, t2;
...
long ns = Duration.between(t1, t2).toNanos();
```
下面代码向一个Instant中添加10秒：
```java
Instant start;
...
Duration gap = Duration.ofSeconds(10);
Instant later = start.plus(gap);
```
Duration未连接到时间轴，因为它不跟踪时区或夏令时。将等于1天的Duration添加到ZonedDateTime会导致正好添加24小时，不管夏令时或其他可能导致的时差。

## ChronoUnit
***
ChronoUnit枚举已经在在Temporal包中讨论过，它定义了用于测量时间的单位。当您想在单个时间单位（如天或秒）中测量一段时间时，ChronoUnit.between方法很有用。between方法可以与所有的基于时间对象一起使用，但是它只返回单个单位的时间。以下代码计算两个时间戳之间的间隔（以毫秒为单位）：
```java
import java.time.Instant;
import java.time.temporal.Temporal;
import java.time.temporal.ChronoUnit;

Instant previous, current, gap;
...
current = Instant.now();
if (previous != null) {
    gap = ChronoUnit.MILLIS.between(previous,current);
}
...
```

## Period
***
要使用基于日期的值（年，月，日）定义一段时间，请使用Period类。Period类提供了各种get方法，例如getMonths，getDays和getYears，以便您可以从Period中提取时间。

总的持续时期由三个单位一起表示：月，天和年。要呈现在单个时间单位（例如天数）中测量的时间量，可以使用ChronoUnit.between方法。

以下代码报告你是多大年龄，假设你是1960年1月1日出生的。Period类用于确定以年，月和日为单位的时间。以总天数表示的时期，通过使用ChronoUnit.between方法确定，并显示在括号中：
```java
LocalDate today = LocalDate.now();
LocalDate birthday = LocalDate.of(1960, Month.JANUARY, 1);

Period p = Period.between(birthday, today);
long p2 = ChronoUnit.DAYS.between(birthday, today);
System.out.println("You are " + p.getYears() + " years, " + p.getMonths() +
                   " months, and " + p.getDays() +
                   " days old. (" + p2 + " days total)");
```
代码产生类似于以下内容的输出：
```java
You are 53 years, 4 months, and 29 days old. (19508 days total)
```
要计算到你下个生日还有多长时间，你可以使用下面Birthday示例中的代码。Period类用于确定以月和日为单位的值。ChronoUnit.between方法返回表示总天数的值，并显示在括号中。
```java
LocalDate birthday = LocalDate.of(1960, Month.JANUARY, 1);

LocalDate nextBDay = birthday.withYear(today.getYear());

//如果你的生日今年已经过过了，那么年数加1
if (nextBDay.isBefore(today) || nextBDay.isEqual(today)) {
    nextBDay = nextBDay.plusYears(1);
}

Period p = Period.between(today, nextBDay);
long p2 = ChronoUnit.DAYS.between(today, nextBDay);
System.out.println("There are " + p.getMonths() + " months, and " +
                   p.getDays() + " days until your next birthday. (" +
                   p2 + " total)");
```
代码产生类似于以下内容的输出：
```
There are 7 months, and 2 days until your next birthday. (216 total)
```
这些计算不考虑时区差异。例如，如果你是出生在澳大利亚，但目前住在班加罗尔，这稍微影响到你确切年龄的计算。在这种情况下，请将Period与ZonedDateTime类结合使用。将Period添加到ZonedDateTime时，会观察时间差异。
***
# Clock
***
> 本节简要介绍了Clock类。您可以使用此类为系统时钟提供替代时钟。

大多数基于时间的对象提供了一个无参数的now()方法，它使用系统时钟和默认时区提供当前的日期和时间。这些基于时间的对象还提供了一个单参数now(Clock)方法，它允许您传递一个替代的Clock。

当前的日期和时间取决于时区，对于全球化的应用程序，一个Clock是必要的，以确保使用正确的时区创建日期/时间。所以，尽管使用Clock类是可选的，但是这个功能允许您测试其他时区的代码，也可以使用固定的时钟，时间不会改变。

Clock类是抽象的，所以你不能创建一个它的实例。以下工厂方法可用于测试。
- Clock.offset(Clock, Duration) 返回一个根据指定Duration偏移的时钟。
- Clock.systemUTC()返回一个表示Greenwich/UTC时区的时钟。
- Clock.fixed(Instant, ZoneId)总是返回相同的Instant。对于这个时钟，时间依旧。

***
# 非ISO日期转换
***
> 本节解释如果将一个ISO日历系统中的日期转换为一个非ISO日历系统日期，比如JapaneseDate或ThaiBuddhistDate。

本教程没有讨论关于java.time.chrono包的任何细节。但是，知道这个包可以提供几种不是基于ISO的预定义年表，比如日本历，伊斯兰历，中华民国历和泰国佛教历。您还可以使用此包来创建自己的年表。

本部分介绍如果将基于ISO和其他预定义日历日期之间的相互转换。
## 转换为非基于ISO日期
***
您可以使用from(TemporalAccessor)方法将基于ISO的日期转换为其他年表的日期，如JapaneseDate.from（TemporalAccessor）。如果无法将日期转换为有效的实例，则此方法将抛出DateTimeException。以下代码将LocalDateTime实例转换为多个预定义的非ISO日历日期：
```java
LocalDateTime date = LocalDateTime.of(2013, Month.JULY, 20, 19, 30);
JapaneseDate jdate     = JapaneseDate.from(date);
HijrahDate hdate       = HijrahDate.from(date);
MinguoDate mdate       = MinguoDate.from(date);
ThaiBuddhistDate tdate = ThaiBuddhistDate.from(date);
```
StringConverter示例将LocalDate转换为ChronoLocalDate到String并返回。 toString方法接收LocalDate和Chronology的实例，并使用提供的Chronology返回转换的字符串。DateTimeFormatterBuilder用于构建可用于打印日期的字符串：
```java
/**
 * 使用提供的年表将一个LocalDate（ISO）值转换为一个ChronoLocalDate日期，
 *然后用一个使用基于该Chronology和当前LocaleSHORT模式的DateTimeFormatter来格式化ChronoLocalDate为一个String。
 *
 * @param localDate - 要转换和格式化的ISO日期。
 * @param chrono - 一个可选年表。如果为null，那么默认使用IsoChronology.
 */
public static String toString(LocalDate localDate, Chronology chrono) {
    if (localDate != null) {
        Locale locale = Locale.getDefault(Locale.Category.FORMAT);
        ChronoLocalDate cDate;
        if (chrono == null) {
            chrono = IsoChronology.INSTANCE;
        }
        try {
            cDate = chrono.date(localDate);
        } catch (DateTimeException ex) {
            System.err.println(ex);
            chrono = IsoChronology.INSTANCE;
            cDate = localDate;
        }
        DateTimeFormatter dateFormatter =
            DateTimeFormatter.ofLocalizedDate(FormatStyle.SHORT)
                             .withLocale(locale)
                             .withChronology(chrono)
                             .withDecimalStyle(DecimalStyle.of(locale));
        String pattern = "M/d/yyyy GGGGG";
        return dateFormatter.format(cDate);
    } else {
        return "";
    }
}
```
当以下日期为预定义的年表调用该方法时：
```java
LocalDate date = LocalDate.of(1996, Month.OCTOBER, 29);
System.out.printf("%s%n",
     StringConverter.toString(date, JapaneseChronology.INSTANCE));
System.out.printf("%s%n",
     StringConverter.toString(date, MinguoChronology.INSTANCE));
System.out.printf("%s%n",
     StringConverter.toString(date, ThaiBuddhistChronology.INSTANCE));
System.out.printf("%s%n",
     StringConverter.toString(date, HijrahChronology.INSTANCE));
```
输出如下所示：
```
10/29/0008 H
10/29/0085 1
10/29/2539 B.E.
6/16/1417 1
```
## 转换为基于ISO日期
***
您可以使用静态LocalDate.from方法将非ISO日期转换为LocalDate实例，如以下示例所示：
```java
LocalDate date = LocalDate.from(JapaneseDate.now());
```
其他基于时间的类也提供了这种方法，如果日期无法转换，它将抛出DateTimeException。

StringConverter示例中的fromString方法，解析一个包含非ISO日期的字符串，并返回一个LocalDate实例。
```java
/**
 * 用一个使用基于当前Local和提供的Chronology的short模式的DateTimeFormatter来解析一个String为一个ChronoLocalDate,
 * 然后将其转换为一个LocalDate（ISO）值
 *
 * @param text   - 该Chronology 和当前Locale预期的输入日期文本。
 *
 * @param chrono - 一个可选Chronology如果为null,那么默认使用IsoChronology。
 */
public static LocalDate fromString(String text, Chronology chrono) {
    if (text != null && !text.isEmpty()) {
        Locale locale = Locale.getDefault(Locale.Category.FORMAT);
        if (chrono == null) {
           chrono = IsoChronology.INSTANCE;
        }
        String pattern = "M/d/yyyy GGGGG";
        DateTimeFormatter df = new DateTimeFormatterBuilder().parseLenient()
                              .appendPattern(pattern)
                              .toFormatter()
                              .withChronology(chrono)
                              .withDecimalStyle(DecimalStyle.of(locale));
        TemporalAccessor temporal = df.parse(text);
        ChronoLocalDate cDate = chrono.date(temporal);
        return LocalDate.from(cDate);
    }
return null;
}
```
当使用以下字符串调用该方法时：
```java
System.out.printf("%s%n", StringConverter.fromString("10/29/0008 H",
    JapaneseChronology.INSTANCE));
System.out.printf("%s%n", StringConverter.fromString("10/29/0085 1",
    MinguoChronology.INSTANCE));
System.out.printf("%s%n", StringConverter.fromString("10/29/2539 B.E.",
    ThaiBuddhistChronology.INSTANCE));
System.out.printf("%s%n", StringConverter.fromString("6/16/1417 1",
    HijrahChronology.INSTANCE));
```
打印的字符串应全部转换为1996年10月29日：
```
1996-10-29
1996-10-29
1996-10-29
1996-10-29
```


***
# 旧的Date-Time代码
***
> 本节提供了有关如何将较旧的java.util.Date和java.util.Calendar代码转换为Date-Time API的一些提示。

在Java SE 8发行版之前，Java日期和时间机制由java.util.Date，java.util.Calendar和java.util.TimeZone类及其子类（如 java.util.GregorianCalendar）提供。这些类有几个缺点，包括：
- Calendar类不是类型安全的。
- 因为类是可变的，所以它们不能用在多线程应用程序。
- 由于特殊的月份和缺乏类型安全，在应用程序代码中bug很常见。

## 与旧版代码的互操作性
***
也许以有些使用java.util日期与时间类的旧的代码，并希望在对你的代码最少改动下利用java.time的功能。

有几个方法被添加到JDK 8版本中，允许你在java.util和java.time对象之间进行转换：
- Calendar.toInstant()将Calendar对象转换为一个Instant。
- GregorianCalendar.toZonedDateTime()将GregorianCalendar对象转换为一个ZonedDateTime。
- GregorianCalendar.from(ZonedDateTime)使用ZonedDateTime实例中的默认区域创建一个GregorianCalendar对象。
- Date.from(Instant)从Instant创建一个Date对象。
- Date.toInstant()将Date对象转换为一个Instant。
- TimeZone.toZoneId()将TimeZone对象转换为一个ZoneId。

下面的示例将一个Calendar实例转换为一个ZonedDateTime实例。注意从Instant转换为ZonedDateTime的时候必须提供一个时区：
```java
Calendar now = Calendar.getInstance();
ZonedDateTime zdt = ZonedDateTime.ofInstant(now.toInstant(), ZoneId.systemDefault()));
```
下面实例显示如何在Date和Instant之间转换：
```java
Instant inst = date.toInstant();

Date newDate = Date.from(inst);
```
下面的示例从一个GregorianCalendar 转换为一个ZonedDateTime，然后从一个ZonedDateTime转换为GregorianCalendar。其他基于时间的类使用ZonedDateTime实例创建：
```java
GregorianCalendar cal = ...;

TimeZone tz = cal.getTimeZone();
int tzoffset = cal.get(Calendar.ZONE_OFFSET);

ZonedDateTime zdt = cal.toZonedDateTime();

GregorianCalendar newCal = GregorianCalendar.from(zdt);

LocalDateTime ldt = zdt.toLocalDateTime();
LocalDate date = zdt.toLocalDate();
LocalTime time = zdt.toLocalTime();
```
## 将java.util的日期和时间功能映射到java.time
***
由于日期和时间的Java实现在Java SE 8版本中已经完全重新设计，因此您无法交换一种方法到另一种方法。如果要使用java.time包提供的丰富功能，最简单的解决方案是使用上一节中列出的toInstant或toZonedDateTime方法。但是，如果您不想使用这种方式或者它不足以满足您的需求，那么你必须重写日期时间代码。

概述页面介绍的表是开始评估哪些java.time类满足您的需求的好地方。

两个API之间没有一对一对应映射关系，但是下面的表格给你一个java.util日期和时间类中哪些功能映射到java.time API的一般概念。

|java.util功能|java.time功能|注释|
|-------------|-------------|----|
|java.util.Date|java.time.Instant|Instan和Date类是类似的。每个类：<br>- 表示时间轴（UTC）上的一个瞬间时间点<br>- 持有一个不依赖时区的时间<br>- 是表示epoch-seconds（自从1970-01-01T00:00:00Z）加上的毫秒<br><br>Date.from(Instant)和Date.toInstant()方法允许在这两个类之间转换|
|java.util.GregorianCalendar|java.time.ZonedDateTime|ZonedDateTime类是GregorianCalendar的替代品。它提供以下类似的功能。<br>如下表示的人类时间<br>LocalDate：年，月，日<br>LocalTime：小时，分钟，秒，毫秒<br>ZoneId：时区<br>ZoneOffset：当前与GMT的偏移<br><br>GregorianCalendar.from(ZonedDateTime)和 GregorianCalendar.to(ZonedDateTime)方法促成这些类之间的转换|
|java.util.TimeZone|java.time.ZoneId或 java.time.ZoneOffset|ZoneId类指定时区标识符，并且可以访问每个时区使用的规则。ZoneOffset类仅指定了与Greenwich/UTC的偏移量。|
|日期设置为1970-01-01的GregorianCalenda|java.time.LocalTime|代码在GregorianCalendar实例中将日期设置为1970-01-01，以便使用的时间组件可以替换为LocalTime的实例。|
|时间设置为00:00的GregorianCalenda|java.time.LocalDate|代码在GregorianCalendar实例中将时间设置为00:00 ，以便使用的时间组件可以替换为LocalDate的实例（由于过渡到夏令时，这个GregorianCalendar方法有缺陷，因为一些国家每年不会发生午夜。）。|

## 日期和时间格式化
***
虽然java.time.format.DateTimeFormatter提供了强大的格式化日期和时间值的机制，但是你也可以将java.time中基于时间的类直接与java.util.Formatter和String.format一起使用，使用你使用java.util日期和时间类一样的基于模式的格式化。


***
# 总结
***
java.time包中包含许多您的程序可用于表示时间和日期的类。这是一个非常丰富的API。基于ISO的日期的关键入口点如下：
- Instant类提供时间线的机器视角。
- LocalDate，LocalTime和LocalDateTime类提供了一个不涉及时区的时间与日期的人类视角。
- ZoneId，ZoneRules和ZoneOffset类描述时区，时区偏移和时区规则。
- ZonedDateTime类表示一个带有时区的日期和时间。OffsetDateTime和OffsetTime类分别表示日期与时间，或时间。这些类考虑到时区偏移。
- Duration类以秒和纳秒为单位测量的时间量。
- Period类使用年，月和日测量一段时间。

其他的非ISO日历系统使用java.time.chrono包表示。尽管“非ISO日期转换”页面提供了有关将基于ISO的日期转换为其他日历系统的信息，但此包超出了本教程的范围。

Date Time API是以JSR 310的名义开发的Java社区进程的一部分。有关更多信息，请参阅[ JSR 310: Date and Time API](http://jcp.org/en/jsr/detail?id=310)。