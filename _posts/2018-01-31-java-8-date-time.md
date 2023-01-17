---
layout: post
title:  Java8新的日期和时间API
date:   2018-01-31 15:51:48
description: 
tags: java
categories: programming
---

## LocalDate
该类的实例是一个不 可变对象，它只提供了简单的日期，并不含当天的时间信息。另外，它也不附带任何与时区相关 10 的信息。
{% highlight java %}
LocalDate date = LocalDate.of(2014, 3, 18);
int year = date.getYear();
Month month = date.getMonth();
int day = date.getDayOfMonth();
DayOfWeek dow = date.getDayOfWeek();
int len = date.lengthOfMonth();
boolean leap = date.isLeapYear();
// 你还可以使用工厂方法从系统时钟中获取当前的日期:
LocalDate today = LocalDate.now();
LocalDate date = LocalDate.parse("2014-03-18");
{% endhighlight %}   

所有日期时间类，这些类都提供了类似的工厂方法。你还可以通过传递一个TemporalField参数给get方法拿到同样的信息。TemporalField是一个接口，它定 义了如何访问temporal对象某个字段的值。ChronoField枚举实现了这一接口，所以你可以很 方便地使用get方法得到枚举元素的值，如下所示。
使用TemporalField读取LocalDate的值
{% highlight java %}
int year = date.get(ChronoField.YEAR);
int month = date.get(ChronoField.MONTH_OF_YEAR);
int day = date.get(ChronoField.DAY_OF_MONTH);
{% endhighlight %}   

## LocalTime
类似地，一天中的时间，比如13:45:20，可以使用LocalTime类表示。你可以使用of重载的 两个工厂方法创建LocalTime的实例。第一个重载函数接收小时和分钟，第二个重载函数同时还 接收秒。
{% highlight java %}
LocalTime time = LocalTime.of(13, 45, 20);
int hour = time.getHour();
int minute = time.getMinute();
int second = time.getSecond();
LocalTime time = LocalTime.parse("13:45:20");
{% endhighlight %}   

## 合并日期和时间LocalDateTime
这个复合类名叫LocalDateTime，是LocalDate和LocalTime的合体。它同时表示了日期
和时间，但不带有时区信息，你可以直接创建，也可以通过合并日期和时间对象构造，
{% highlight java %}
// 2014-03-18T13:45:20
LocalDateTime dt1 = LocalDateTime.of(2014, Month.MARCH, 18, 13, 45, 20); LocalDateTime dt2 = LocalDateTime.of(date, time);
LocalDateTime dt3 = date.atTime(13, 45, 20);
LocalDateTime dt4 = date.atTime(time);
LocalDateTime dt5 = time.atDate(date);
LocalDate date1 = dt1.toLocalDate();
LocalTime time1 = dt1.toLocalTime();
{% endhighlight %}   

## Instant
新的java.time.Instant类对时间建模的方 式，基本上它是以Unix元年时间(传统的设定为UTC时区1970年1月1日午夜时分)开始所经历的 秒数进行计算。
{% highlight java %}
Instant.ofEpochSecond(3);
Instant.ofEpochSecond(3, 0);
Instant.ofEpochSecond(2, 1_000_000_000);
Instant.ofEpochSecond(4, -1_000_000_000);
Instant.now()
{% endhighlight %}   

## Duration
可以创建两个LocalTimes对象、两个LocalDateTimes对象，或者两个Instant对象之间的duration，如下所示:
{% highlight java %}
Duration d1 = Duration.between(time1, time2);
Duration d1 = Duration.between(dateTime1, dateTime2);
Duration d2 = Duration.between(instant1, instant2);

Duration threeMinutes = Duration.ofMinutes(3);
Duration threeMinutes = Duration.of(3, ChronoUnit.MINUTES);
{% endhighlight %}   

由于LocalDateTime和Instant是为不同的目的而设计的，一个是为了便于人阅读使用， 另一个是为了便于机器处理，所以你不能将二者混用。
## Period
Period类。使用该类的工厂方法between，你可以使用得到两个LocalDate之间的时长，如下所示:
{% highlight java %}
Period tenDays = Period.between(LocalDate.of(2014, 3, 8), LocalDate.of(2014, 3, 18));
Period tenDays = Period.ofDays(10);
Period threeWeeks = Period.ofWeeks(3);
Period twoYearsSixMonthsOneDay = Period.of(2, 6, 1);
{% endhighlight %}   


## 操纵、解析和格式化日期
* withAttribute方法会创建对象的一个副本，并按照需要修改它的属性。
{% highlight java %}
LocalDate date1 = LocalDate.of(2014, 3, 18);
LocalDate date2 = date1.withYear(2011);
LocalDate date3 = date2.withDayOfMonth(25);
LocalDate date4 = date3.with(ChronoField.MONTH_OF_YEAR, 9);
{% endhighlight %}   

* 以相对方式修改LocalDate对象的属性
{% highlight java %}
LocalDate date1 = LocalDate.of(2014, 3, 18);
LocalDate date2 = date1.plusWeeks(1);
LocalDate date3 = date2.minusYears(3);
LocalDate date4 = date3.plus(6, ChronoUnit.MONTHS);
{% endhighlight %}   

* TemporalAdjuster
{% highlight java %}
// 使用预定义的TemporalAdjuster
import static java.time.temporal.TemporalAdjusters.*;
LocalDate date1 = LocalDate.of(2014, 3, 18);
LocalDate date2 = date1.with(nextOrSame(DayOfWeek.SUNDAY)); 
LocalDate date3 = date2.with(lastDayOfMonth());
{% endhighlight %}   
 
{% highlight java %}
// TemporalAdjuster接口
@FunctionalInterface
public interface TemporalAdjuster {
    Temporal adjustInto(Temporal temporal);
}
{% endhighlight %}   


## DateTimeFormatter
格式化以及解析日期时间对象是另一个非常重要的功能。新的 java.time.format包就是特别为这个目的而设计的。
和老的java.util.DateFormat相比较，所有的DateTimeFormatter实例都是线程安全 的。
* 创建格式器最简单的方法是通过它的静态工厂方法以及常量
{% highlight java %}
LocalDate date = LocalDate.of(2014, 3, 18);
String s1 = date.format(DateTimeFormatter.BASIC_ISO_DATE);
String s2 = date.format(DateTimeFormatter.ISO_LOCAL_DATE);

LocalDate date1 = LocalDate.parse("20140318", DateTimeFormatter.BASIC_ISO_DATE);
LocalDate date2 = LocalDate.parse("2014-03-18", DateTimeFormatter.ISO_LOCAL_DATE);
{% endhighlight %}   

* 按照某个模式创建DateTimeFormatter
{% highlight java %}
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy");
LocalDate date1 = LocalDate.of(2014, 3, 18);
String formattedDate = date1.format(formatter);
LocalDate date2 = LocalDate.parse(formattedDate, formatter);
{% endhighlight %}   

* 创建一个本地化的DateTimeFormatter
{% highlight java %}
DateTimeFormatter italianFormatter = DateTimeFormatter.ofPattern("d. MMMM yyyy", Locale.ITALIAN);
LocalDate date1 = LocalDate.of(2014, 3, 18);
String formattedDate = date.format(italianFormatter); // 18. marzo 2014 
LocalDate date2 = LocalDate.parse(formattedDate, italianFormatter);
{% endhighlight %}   

* DateTimeFormatterBuilder
如果你还需要更加细粒度的控制，DateTimeFormatterBuilder类还提供了更复杂 的格式器，你可以选择恰当的方法，一步一步地构造自己的格式器。

{% highlight java %}
DateTimeFormatter italianFormatter = new DateTimeFormatterBuilder().appendText(ChronoField.DAY_OF_MONTH)
.appendLiteral(". ")
.appendText(ChronoField.MONTH_OF_YEAR)
.appendLiteral(" ")
.appendText(ChronoField.YEAR)
.parseCaseInsensitive()
.toFormatter(Locale.ITALIAN);
{% endhighlight %}   

## 处理不同的时区和历法
新的java.time.ZoneId 类是老版java.util.TimeZone的替代品。跟其他日期和时间类一 样，ZoneId类也是无法修改的。
{% highlight java %}
ZoneId romeZone = ZoneId.of("Europe/Rome");
{% endhighlight %}   

* 为时间点添加时区信息
{% highlight java %}
LocalDate date = LocalDate.of(2014, Month.MARCH, 18); 6
ZonedDateTime zdt1 = date.atStartOfDay(romeZone);
{% endhighlight %}

* 通过ZoneId，你还可以将LocalDateTime转换为Instant:
{% highlight java %}
LocalDateTime dateTime = LocalDateTime.of(2014, Month.MARCH, 18, 13, 45); 
Instant instantFromDateTime = dateTime.toInstant(romeZone);

Instant instant = Instant.now();
LocalDateTime timeFromInstant = LocalDateTime.ofInstant(instant, romeZone);
{% endhighlight %}   

## 使用别的日历系统
ISO-8601日历系统是世界文明日历系统的事实标准。但是，Java 8中另外还提供了4种其他的 日历系统。这些日历系统中的每一个都有一个对应的日志类，分别是ThaiBuddhistDate、 MinguoDate 、 JapaneseDate 以 及 HijrahDate 。 所 有 这 些 类 以 及 LocalDate 都 实 现 了 ChronoLocalDate接口，能够对公历的日期进行建模。
{% highlight java %}
LocalDate date = LocalDate.of(2014, Month.MARCH, 18);
JapaneseDate japaneseDate = JapaneseDate.from(date);

Chronology japaneseChronology = Chronology.ofLocale(Locale.JAPAN); 
ChronoLocalDate now = japaneseChronology.dateNow();
{% endhighlight %}   

