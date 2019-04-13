# java8之新版日期时间

Java8 增加了常用的以下几个新类：LocalDate 、LocalTime，Period，Instant，DateTimeFormatter等类。

## LocalDate、LocalTime

其中LocalDate主要用来表示日期：年 月 日。LocalTime用来表示时间：时 分 秒。当然也有2者的结合类：LocalDateTIme表示日期时间结合体。

**LocalDate**

它是不可变对象，是无法更改的。

创建方式：

```java
LocalDate date = LocalDate.now();//根据当前日期进行创建
LocalDate date = LocalDate.of(2014,4,6);//指定年、月、日
```

根据已有的LocalDate对象进行更改：如使用一列的with方法，或者使用加减方法，plus，minus等

```java
LocalDate date2 = date.withYear(2011)；
LocalDate date3 = date.with(ChronoField.MONTH_OF_YEAR, 9);//通用的方法，可使用ChronoField来指定改变年、月、日，还是其他。

LocalDate date4 = date.plus(ChronoUnit.Days, 8);//在date的基础上加8天
```

>ChronoField 用来获取日期，里面都是MONTH_OF_YEAR这些方便获取已有日期的信息的枚举常量
>
>ChronoUnit 一般用来加减时使用，例如ChronoUnit.Days，ChronoUnit。Year

也可以使用parse方法，将已有的字符串按照一定的格式转化为LocalDate对象，可指定格式转化器。

```java
LocalDate d = LocalDate.parse("2018-09-07", DateTimeFormatter.ofPattern("yyyy-MM-dd"));
```

其他常用方法：

```java
/**
	一系列转化为LocalDateTime对象的方法
*/
LocalDateTime atStartOfDay();
LocalDateTime atTime(int hour, int minute, int second, int nanoOfSecond);
LocalDateTime atTime(LocalTime time);
/**
	常用的时间比较的方法
*/
boolean isAfter(ChronoLocalDate other);
boolean isBefore(ChronoLocalDate other);
boolean isEqual(ChronoLocalDate other);
boolean isLeapYear(); //判断平年or闰年
/**
 计算2个日期间的差值。
*/
long daysUntil(LocalDate end);//计算2个日期之间相差多少天
Period Until(ChronoLocalDate endDateExclusive);//将2个日期转化为Period对象
long until(Temporal endExclusive, TemporalUnit unit);//将差值转化为年、月等，请注意此处只取整，如果不满一年，则返回0；

long toEpochDay();//计算从1970年之后的天数
```

**LocalTime**

LocalTime基本用法与LocalDate相似。

## Period、Instant

Period主要用来处理2个LocalDate类型的差值。同样也是 **不可变对象**。

**创建方式**：

它本身的构造方法是私有的，所以我们只能通过其本身的提供的静态方法创建Period对象。

```java
static Period between(LocalDate startDateInclusive, LocalDate endDateExclusive);
static Period of(int years, int months, int days);
// 一系列的of的重载方法
```

> 我们需要注意使用p.getDays()，等这些方法时，它返回的不是将时间间隔换算成有几个月。而是该Period对象在创建的时候，指定的月。

of方法是创建对象，而with一系列的方法，是基于现有的Period对象进行更改。

例如：

```java
Period p = Period.of(2,3,4);//使用of方法初始化对象
Period p2 = p.withDays(5);//则p2的对象为 2 3 5；
```

它也有一列的```plus```、```minus```的方法。

## DateTimeFormatter(没有深入研究里面的源代码)

这个类相当于之前的类SimpleDateFormatter，不过他是 **线程安全的**。

其中最常用的就是定义格式的转化。

```java
DateTimeFormatter dtf = DateTimeFormatter.ofPattern
                ("yyyy-M-d", Locale.US);
```

我们可以使用```ofPatter```方法来自定义格式。当然可以使用里面内置了常用的格式对象。



## TemporalAdjuster

它是一个 **函数式接口**，用来调整时间，例如使用```Localdate. with(TemporalAdjuster adjuster)```。

例子：返回明天的日期，但是需要过滤掉周六、周日。

代码如下：

```java
LocalDate d1 = LocalDate.of(2019, 4, 6);

d1 = d1.with(r -> {
  int plusDays = 1;
  DayOfWeek week = DayOfWeek.of(r.get(ChronoField.DAY_OF_WEEK));
  if (week == DayOfWeek.FRIDAY) {
    plusDays = 3;
  }
  if (week == DayOfWeek.SATURDAY) {
    plusDays = 2;
  }
  return r.plus(plusDays, ChronoUnit.DAYS);

});
```

**TemporalAdjusters**这个类似于工具类的工具，定义了一些常用的调整的方法。

## 时区ZoneId

每个时区都由{区域}/{城市}的格式指定。<font color=red>但是里面有哪几种组合还不清楚。</font>

例如：

```java
ZoneId zoneId = ZoneId.of("Europe/Rome");
```

使用方式：

```java
// 时区
ZoneId zoneId = ZoneId.of("Europe/Rome");
ZonedDateTime zdt1 = d1.atStartOfDay(zoneId);
System.out.println(d1);
```

