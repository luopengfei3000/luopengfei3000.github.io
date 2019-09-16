---
layout: post
title: java8:新的日期和日期API
no-post-nav: true
category: java
tags: [java]
keywords: java
excerpt : java8:新的日期和日期API
---

### java8:新的日期和日期API

Java 8的日期和时间类包含 LocalDate、LocalTime、Instant、Duration 以及 Period，这些类都包含在 java.time 包中，
Java 8 新的时间API的使用方式，包括创建、格式化、解析、计算、修改，下面我们看下如何去使用。

LocalDate 只会获取年月日

``` java
    // 获取当前年月日
    LocalDate localDate = LocalDate.now();
    // 构造指定的年月日
    LocalDate localDate1 = LocalDate.of(2019, 9, 12);
    // 获取年、月、日、星期几
    int year = localDate.getYear();
    int year1 = localDate.get(ChronoField.YEAR);
    System.out.println("year:"+year +"---year1:"+year1);
    
    Month month = localDate.getMonth();
    int month1 = localDate.get(ChronoField.MONTH_OF_YEAR);
    System.out.println("month:"+month+"/"+month.getValue() +"---month1:"+month1);
    
    int day = localDate.getDayOfMonth();
    int day1 = localDate.get(ChronoField.DAY_OF_MONTH);
    System.out.println("day:"+day +"---day1:"+day1);
    
    DayOfWeek dayOfWeek = localDate.getDayOfWeek();
    int dayOfWeek1 = localDate.get(ChronoField.DAY_OF_WEEK);
    System.out.println("dayOfWeek为星期:"+dayOfWeek+"/"+dayOfWeek.getValue() +"---dayOfWeek1为星期:"+dayOfWeek1);
```

``` java
    year:2019---year1:2019
    month:SEPTEMBER/9---month1:9
    day:16---day1:16
    dayOfWeek为星期:MONDAY/1---dayOfWeek1为星期:1
```
LocalTime 只会获取时分秒

``` java
    // 创建 LocalTime
    LocalTime localTime = LocalTime.of(14, 14, 14);
    //14:14:14
    LocalTime localTime1 = LocalTime.now();
    //11:38:33.594
    // 获取小时
    int hour = localTime.getHour();
    int hour1 = localTime.get(ChronoField.HOUR_OF_DAY);
    // 获取分
    int minute = localTime.getMinute();
    int minute1 = localTime.get(ChronoField.MINUTE_OF_HOUR);
    // 获取秒
    int second = localTime.getMinute();
    int second1 = localTime.get(ChronoField.SECOND_OF_MINUTE);
```

LocalDateTime 获取年月日时分秒，相当于 LocalDate + LocalTime

``` java
    LocalDate localDate = LocalDate.now();
    LocalTime localTime = LocalTime.now();
    //创建 LocalDateTime
    LocalDateTime localDateTime = LocalDateTime.now();
    //2019-09-16T11:42:41.036
    LocalDateTime localDateTime1 = LocalDateTime.of(2019, Month.SEPTEMBER, 10, 14, 46, 56);
    //2019-09-10T14:46:56
    LocalDateTime localDateTime2 = LocalDateTime.of(localDate, localTime);
    //2019-09-16T11:42:41.036
    LocalDateTime localDateTime3 = localDate.atTime(localTime);
    //2019-09-16T11:42:41.036
    LocalDateTime localDateTime4 = localTime.atDate(localDate);
    //2019-09-16T11:42:41.036
    //获取LocalDate
    LocalDate localDate2 = localDateTime.toLocalDate();
    //2019-09-16
    //获取LocalTime
    LocalTime localTime2 = localDateTime.toLocalTime();
    //11:42:41.036
```
Instant 获取秒数，用于表示一个时间戳（精确到纳秒）

``` java
    // 创建Instant对象
    Instant instant = Instant.now();
    // 获取秒数
    long currentSecond = instant.getEpochSecond();
    // 获取毫秒数
    long currentMilli = instant.toEpochMilli();
```

Duration 表示一个时间段

``` java
    // Duration.between()方法创建 Duration 对象
    LocalDateTime from = LocalDateTime.of(2017, Month.JANUARY, 1, 00, 0, 0);    // 2017-01-01 00:00:00
    LocalDateTime to = LocalDateTime.of(2019, Month.SEPTEMBER, 12, 14, 28, 0);     // 2019-09-15 14:28:00
    Duration duration = Duration.between(from, to);     // 表示从 from 到 to 这段时间
    long days = duration.toDays();              // 这段时间的总天数
    long hours = duration.toHours();            // 这段时间的小时数
    long minutes = duration.toMinutes();        // 这段时间的分钟数
    long seconds = duration.getSeconds();       // 这段时间的秒数
    long milliSeconds = duration.toMillis();    // 这段时间的毫秒数
    long nanoSeconds = duration.toNanos();
```

修改 LocalDate、LocalTime、LocalDateTime、Instant。

LocalDate、LocalTime、LocalDateTime、Instant 为不可变对象，修改这些对象对象会返回一个副本。

增加、减少年数、月数、天数等，以LocalDateTime为例:

``` java
    LocalDateTime localDateTime = LocalDateTime.of(2019, Month.SEPTEMBER, 12, 14, 32, 0);
    // 增加一年
    localDateTime = localDateTime.plusYears(1);
    localDateTime = localDateTime.plus(1, ChronoUnit.YEARS);
    // 减少一个月
    localDateTime = localDateTime.minusMonths(1);
    localDateTime = localDateTime.minus(1, ChronoUnit.MONTHS);  
    // 通过with修改某些值
    // 修改年为2020
    localDateTime = localDateTime.withYear(2020);
    localDateTime = localDateTime.with(ChronoField.YEAR, 2020);
    // 时间计算
    // 获取该年的第一天
    LocalDate localDate = LocalDate.now();
    LocalDate localDate1 = localDate.with(TemporalAdjusters.firstDayOfYear());
```
TemporalAdjusters 包含许多静态方法，可以直接调用，以下列举一些：

| 方法名 | 描述 |
| ------------- |: ------------- :|
dayOfWeekInMonth | 返回同一个月中每周的第几天 |
firstDayOfMonth | 返回当月的第一天 |
firstDayOfNextMonth | 返回下月的第一天 |
firstDayOfNextYear | 返回下一年的第一天 |
firstDayOfYear | 返回本年的第一天 |
firstInMonth | 返回同一个月中第一个星期几 |
lastDayOfMonth | 返回当月的最后一天 |
lastDayOfNextMonth | 返回下月的最后一天 |
lastDayOfNextYear | 返回下一年的最后一天 |
lastDayOfYear | 返回本年的最后一天 |
lastInMonth | 返回同一个月中最后一个星期几 |
next / previous | 返回后一个/前一个给定的星期几 |
nextOrSame / previousOrSame | 返回后一个/前一个给定的星期几，如果这个值满足条件，直接返回 |

格式化时间

``` java
    LocalDate localDate = LocalDate.of(2019, 9, 12);
    String s1 = localDate.format(DateTimeFormatter.BASIC_ISO_DATE);
    String s2 = localDate.format(DateTimeFormatter.ISO_LOCAL_DATE);
    // 自定义格式化
    DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
    String s3 = localDate.format(dateTimeFormatter);
```

解析时间

``` java
    LocalDate localDate1 = LocalDate.parse("20190912", DateTimeFormatter.BASIC_ISO_DATE);
    LocalDate localDate2 = LocalDate.parse("2019-09-12", DateTimeFormatter.ISO_LOCAL_DATE);
```

总结

- 和SimpleDateFormat相比，DateTimeFormatter是线程安全的。

- Instant 的精确度更高，可以精确到纳秒级。

- Duration 可以便捷得到时间段内的天数、小时数等。

- LocalDateTime 能够快速地获取年、月、日、下一月等。

- TemporalAdjusters 类中包含许多常用的静态方法，避免自己编写工具类。



