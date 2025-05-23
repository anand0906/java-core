# Date and Time API: (Joda-Time API)

Until Java 1.7, the classes present in `java.util` package to handle Date and Time (like `Date`, `Calendar`, `TimeZone`, etc.) were not efficient in terms of convenience and performance. 
To overcome this problem, in Java SE 8, Oracle introduced the **Joda-Time API**, available in the `java.time` package.

## Displaying System Date and Time
```java
import java.time.*;
public class DateTime {
    public static void main(String[] args) {
        LocalDate date = LocalDate.now();
        System.out.println(date);
        LocalTime time = LocalTime.now();
        System.out.println(time);
    }
}
```
**Output:**
```
2025-02-28
12:39:26.587
```

## Extracting Day, Month, and Year from `LocalDate`
```java
import java.time.*;
class Test {
    public static void main(String[] args) {
        LocalDate date = LocalDate.now();
        System.out.println(date);
        int dd = date.getDayOfMonth();
        int mm = date.getMonthValue();
        int yy = date.getYear();
        System.out.println(dd + "..." + mm + "..." + yy);
        System.out.printf("%d-%d-%d", dd, mm, yy);
    }
}
```

## Extracting Time Components from `LocalTime`
```java
import java.time.*;
class Test {
    public static void main(String[] args) {
        LocalTime time = LocalTime.now();
        int h = time.getHour();
        int m = time.getMinute();
        int s = time.getSecond();
        int n = time.getNano();
        System.out.printf("%d:%d:%d:%d", h, m, s, n);
    }
}
```

## Combining Date and Time
```java
LocalDateTime dt = LocalDateTime.now();
System.out.println(dt);
```
**Output:**
```
2025-02-28T12:57:24.531
```

### Creating a Specific Date and Time
```java
LocalDateTime dt1 = LocalDateTime.of(1995, Month.APRIL, 28, 12, 45);
System.out.println(dt1);
```

### Manipulating Date and Time
```java
LocalDateTime dt1 = LocalDateTime.of(1995, 4, 28, 12, 45);
System.out.println("After six months: " + dt1.plusMonths(6));
System.out.println("Before six months: " + dt1.minusMonths(6));
```

## Working with Time Zones
```java
import java.time.*;
class ProgramOne {
    public static void main(String[] args) {
        ZoneId zone = ZoneId.systemDefault();
        System.out.println(zone);
    }
}
```

### Creating a Zone-Specific Date-Time
```java
ZoneId la = ZoneId.of("America/Los_Angeles");
ZonedDateTime zt = ZonedDateTime.now(la);
System.out.println(zt);
```

## Representing Periods
A `Period` object represents a quantity of time.
```java
LocalDate today = LocalDate.now();
LocalDate birthday = LocalDate.of(1989, 6, 15);
Period p = Period.between(birthday, today);
System.out.printf("Age is %d years %d months %d days", p.getYears(), p.getMonths(), p.getDays());
```

## Checking for Leap Year
```java
import java.time.*;
public class LeapYear {
    public static void main(String[] args) {
        int n = Integer.parseInt(args[0]);
        Year y = Year.of(n);
        if (y.isLeap())
            System.out.printf("%d is a Leap Year", n);
        else
            System.out.printf("%d is not a Leap Year", n);
    }
}
```

With this, Java SE 8 provides a much more **efficient, thread-safe, and immutable** way to handle date and time operations compared to previous Java versions.

