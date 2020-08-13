# 新的时间API

旧的时间API缺点：可变，多线程不安全

- SimpleDateFormat：时间格式转换
- Date：时间
- 如果需要在多线程环境下使用，需要使用ThreadLocal

新的时间API：不可变，多线程安全

- DateTimeFormatter
- LocalDate

```java
public class TestTime {
    static class DateFormatThreadLocal {
        private static final ThreadLocal<DateFormat> df = new ThreadLocal<DateFormat>() {
            @Override
            protected DateFormat initialValue() {
                return new SimpleDateFormat("yyyyMMdd");
            }
        };

        public static Date convert(String source) throws ParseException {
            return df.get().parse(source);
        }
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 传统时间在多线程环境下的使用
        Callable<Date> call = () -> DateFormatThreadLocal.convert("20200813");

        ExecutorService pool = Executors.newFixedThreadPool(10);
        List<Future<Date>> futures = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            futures.add(pool.submit(call));
        }

        // 新的时间API
        // 时间格式转换API：DateTimeFormatter
        DateTimeFormatter dtf =
                DateTimeFormatter.ofPattern("yyyyDDmm");
        // 新的时间API：LocalDate
        Callable<LocalDate> task = () -> LocalDate.parse("20200813", dtf);
        List<Future<LocalDate>> futures1 = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            futures1.add(pool.submit(task));
        }

        // 结果输出
        for (int i = 0; i < 5; i++) {
            System.out.println(futures.get(i).get());
            System.out.println(futures1.get(i).get());
        }
        pool.shutdown();
    }
}
// 输出：
Thu Aug 13 00:00:00 CST 2020
2020-01-08
Thu Aug 13 00:00:00 CST 2020
2020-01-08
Thu Aug 13 00:00:00 CST 2020
2020-01-08
Thu Aug 13 00:00:00 CST 2020
2020-01-08
Thu Aug 13 00:00:00 CST 2020
2020-01-08
```

新的时间API：

- LocalDate、LocalTime、LocalDateTime类的实例是不可变的对象，分别表示使用ISO-8601日历系统的日期、时间、日期和时间。它们提供了简单的日期或时间，并不包含当前的时间新。也不包含与时区相关的信息

- Instant：时间戳（以Unix元年：1970年1月1日 00:00:00到某个时间之间的毫秒值）

- Duration和Period：间隔

  - Duration：时间间隔，用于LocalTime和Instant
  - Period：日期间隔，用于LocalDate

  ```java
  @org.junit.Test
  public void testTime() {
      // LocalDate、LocalTime、LocalDateTime
      // 获取当前时间：LocalDateTime.now()
      LocalDateTime ldt = LocalDateTime.now();
      System.out.println(ldt);
      // 指定一个时间：LocalDateTime.of()
      LocalDateTime ldt1 = LocalDateTime.of(LocalDate.now(), LocalTime.now());
      System.out.println(ldt1);
  
      // 时间日期操作：
      // 1.增加：LocalDateTime.plusDays()
      // 2.减去：LocalDateTime.minusHours()
      // 3.获取：LocalDateTime.getMonth()
      System.out.println(ldt1.plusDays(20)
                         .minusHours(12).getMonth());
  
      // Instant：时间戳（以Unix元年：1970年1月1日 00:00:00到某个时间之间的毫秒值）
      Instant now = Instant.now(); // 获取当前时间，默认获取UTC时区
      // Instant.ofEpochMilli()使用毫秒获得一个实例Instant，起始值Unix元年
      // now.toEpochMilli()：时间戳
  
      // OffsetDateTime：2020-08-13T17:53:18.985+08:00
      OffsetDateTime offsetDateTime = now.atOffset(ZoneOffset.ofHours(8));
      System.out.println(offsetDateTime);
  
      // Duration：计算两个"时间"之间的间隔，用于Instant或LocalTime
      // Period：计算两个"日期"之间的间隔，用于LocalDate
      Instant start = Instant.now();
      Instant end =Instant.now();
      Duration duration = Duration.between(start,end);
      // 将时间间隔以ms输出：duration.toMillis()
      System.out.println(duration.toMillis());
      LocalDate dt1 = LocalDate.of(2019, 2, 23);
      LocalDate dt2 = LocalDate.now();
      Period period = Period.between(dt1, dt2);
      System.out.println(period.getYears());
  }
  ```

日期的操纵：

- TemporalAdjuster：时间校正器。用于某些可能的日期获取操作，比如获取下个周日的操作
- TemporalAdjusters：该类通过静态方法提供了大量的常用TemporalAdjuster的实现

格式化时间/日期：DateTimeFormatter

```java
@org.junit.Test
public void testAdjustTime(){
    LocalDate ld = LocalDate.now();
    // 将日期调整为几号
    System.out.println(ld.withDayOfMonth(10));
    // TemporalAdjusters
    ld.with(TemporalAdjusters.lastDayOfYear());

    // DateTimeFormatter：格式化时间/日期
    DateTimeFormatter dtf = DateTimeFormatter.ISO_DATE_TIME;
    DateTimeFormatter dtf1 = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss");
    LocalDateTime ldt = LocalDateTime.now();
    String str = ldt.format(dtf);
    String format = ldt.format(dtf1);
    System.out.println(str);
    System.out.println(format);
}
```

时区操作：

```java
@org.junit.Test
public void testZone() {
    // ZonedDate、ZonedTime、ZoneDateTime：时区操作
    // 1.获取所有可用时区
    Set<String> set = ZoneId.getAvailableZoneIds();
    // 2.获取指定时区的当前时间
    LocalDateTime now = LocalDateTime.now(ZoneId.of("Etc/GMT-8"));
    // 将LocalDateTime转换成带特定的时区的时间
    ZonedDateTime zdt = now.atZone(ZoneId.of("Asia/Shanghai"));
}
```

