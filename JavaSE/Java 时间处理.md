# Java 时间处理 Joda Time

官方文档：https://www.joda.org/joda-time/userguide.html#

> Joda-Time提供了Java日期和时间类的质量替代。
>
> Joda-Time是Java SE 8之前的Java的*事实上的*标准日期和时间库。现在，要求用户迁移到`java.time`（JSR-310）。
>
> Joda-Time已获得商业友好的[Apache 2.0许可](https://www.joda.org/joda-time/licenses.html)。

为什么使用JodaTime

> Java SE 8之前的标准日期和时间类很差。通过直接解决此问题，Joda-Time成为Java SE 8之前的Java的事实上的标准日期和时间库。**请注意，从Java SE 8开始，要求用户迁移到`java.time`（JSR-310） -JDK的核心部分，它将取代该项目。**
>
> 该设计允许使用多个日历系统，同时仍提供一个简单的API。“默认”日历是许多其他标准使用的[ISO8601](https://www.joda.org/joda-time/cal_iso.html)标准。格里高利，朱利安，佛教，科普特，埃塞俄比亚和伊斯兰历法系统也包括在内。支持类包括时区，持续时间，格式和解析。

pom依赖

```
<dependency>
    <groupId>joda-time</groupId>
    <artifactId>joda-time</artifactId>
    <version>2.9.9</version>
</dependency>
```

简单的demo

```java
public class DateTimeUtil {

    //joda-time

    //str->Date
    //Date->str
    public static final String STANDARD_FORMAT = "yyyy-MM-dd HH:mm:ss";

    public static Date strToDate(String dateTimeStr,String formatStr){
        DateTimeFormatter dateTimeFormatter = DateTimeFormat.forPattern(formatStr);
        DateTime dateTime = dateTimeFormatter.parseDateTime(dateTimeStr);
        return dateTime.toDate();
    }

    public static String dateToStr(Date date,String formatStr){
        if(date == null){
            return StringUtils.EMPTY;
        }
        DateTime dateTime = new DateTime(date);
        return dateTime.toString(formatStr);
    }

    public static Date strToDate(String dateTimeStr){
        DateTimeFormatter dateTimeFormatter = DateTimeFormat.forPattern(STANDARD_FORMAT);
        DateTime dateTime = dateTimeFormatter.parseDateTime(dateTimeStr);
        return dateTime.toDate();
    }

    public static String dateToStr(Date date){
        if(date == null){
            return StringUtils.EMPTY;
        }
        DateTime dateTime = new DateTime(date);
        return dateTime.toString(STANDARD_FORMAT);
    }

    public static void main(String[] args) {
        System.out.println(DateTimeUtil.dateToStr(new Date(),"yyyy-MM-dd HH:mm:ss"));
        System.out.println(DateTimeUtil.strToDate("2010-01-01 11:11:11","yyyy-MM-dd HH:mm:ss"));

    }
}
```

```java
import org.joda.time.DateTime;
import org.joda.time.LocalDate;

public class JodaTest2 {
    public static void main(String[] args) {
        DateTime today = new DateTime();
        DateTime datetorrow = today.plusDays(1);

        System.out.println(today.toString("yyyy-MM-dd"));//2017-06-26
        System.out.println(today.toString("yyyy-MM-dd HH:mm:ss"));//2017-06-26 22:04:03
        System.out.println(datetorrow.toString("yyyy-MM-dd"));//2017-06-27

        System.out.println("......................");

        //获得一个时间的副本，将day设置成自己制定的时间,不改变月份，只改变日期
        DateTime d1 = today.withDayOfMonth(1);
        System.out.println(d1.toString("yyyy-MM-dd"));//2017-06-01

        System.out.println("......................");

        LocalDate localDate = new LocalDate();
        System.out.println(localDate);//2017-06-26

        System.out.println("........................");

        //获取当前时间三个月后的月份的最后一天
        localDate = localDate.plusMonths(3).dayOfMonth().withMaximumValue();
        System.out.println(localDate);//2017-09-30

        System.out.println("........................");


        //计算二年前第三个月最后一天的日期
        DateTime dateTime = new DateTime();
        DateTime dateTime2 = dateTime.minusYears(2).monthOfYear().setCopy(3).
                dayOfMonth().withMinimumValue();
        System.out.println(dateTime2.toString("yyyy-MM-dd"));//2017-09-30

    }
}


public class JodaTest3 {

    //将utc时间转换成java中的Date格式
    public static Date convertUTC2Date(String utcDate){
        try{
            DateTime dateTime =DateTime.parse(utcDate, DateTimeFormat.forPattern("yyyy-MM-dd'T'HH:mm:ss.SSSZ"));
            return dateTime.toDate();
        }catch (Exception ex){
            return null;
        }
    }

    //将java中的date格式的时间转换成utc时间标准
    public static String ConvertDate2UTC(Date javaDate){
        DateTime dateTime = new DateTime(javaDate, DateTimeZone.UTC);
        return dateTime.toString();
    }

    //将Date类型转换成字符串
    public static String convertDate2LocalByDateformat(Date javaDate,String dateFormat){
        DateTime dateTime = new DateTime(javaDate);
        return dateTime.toString(dateFormat);
    }


    public static void main(String[] args) {
        System.out.println(JodaTest3.convertUTC2Date("2010-12-1T11:22:33.567Z"));//Wed Dec 01 19:22:33 CST 2010
        System.out.println(JodaTest3.ConvertDate2UTC(new Date()));//2017-06-26T14:09:53.606Z
        System.out.println(JodaTest3.convertDate2LocalByDateformat(new Date(),"yyyy-MM-dd HH:mm:ss"));//2017-06-26 22:09:53
    }
}
```

