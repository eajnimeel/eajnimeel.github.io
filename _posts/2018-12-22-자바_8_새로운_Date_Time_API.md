---
title: 자바 8의 새로운 날짜/시간 API
categories: [java]
tags: [java8]
---

`java.time` 패키지에 8이전에서 제공하던 날짜와 시간에 관련된 API의 문제점을 개선한 새로운 클래스들이 포함됨.
기존에 문제점들은 일반적으로 익숙하지 않을 동시성 문제라던가, 월은 0부터 시작하지만 일은 1부터 시작하는 등의 이상한 설계 등이 있다고 함

```java
// java 8 이전
import java.Util.Date;

Date d = new Date(); // 현재의 Date 인스턴스
d.setMonth(1); // 현재 Date 인스턴스에서 월을 2로 변경

// java 8
import java.time.*;

LocalDate ld = LocalDate.now(); // 현재의 LocalDate 인스턴스
ld.withMonth(1); // ld에서 월을 1월로 변경한 인스턴스 반환. Date 객체의 인스턴스인 d가 변경된 것과 달리 LocalDate 객체의 인스턴스인 ld는 변경이 안 됨.
```

이전과 달리 `LocalDate`, `LocalTime`, `LocalDateTime` 객체는 불변 객체이기 때문에, 직접 인스턴스의 값을 변경할 수 없음.
따라서 계산이 필요한 경우 API 호출시 반환되는 인스턴스를 받아서 써야 함.

```java
import java.time.*;
import java.time.temporal.*;

LocalDate currentDate = LocalDate.now();
LocalDate xmas = LocalDate.of(2018, 12, 25); // 2018-12-25
LocalDate xmas2 = LocalDate.parse("2018-12-25"); // 2018-12-25
LocalDate xmas3 = currentDate.withMonth(12).withDayOfYear(25);
LocalDate xmasEve = xmas.minusDays(1); // 2018-12-24
LocalDate xmaxEve2 = xmas.minus(1, ChronoUnit.DAYS); // 2018-12-24

LocalTime lunchStartTime = LocalTime.of(12, 30); // 12:00
LocalTime lunchEndTime = lunchStartTime.plusHours(1); // 13:30
LocalTime startTime = LocalTime.parse("09:00"); // 09:00
LocalTime closingTime = startTime.withHour(18); // 18:00

int year = xmas.getYear()
Month monthOfXmas = xmas.getMonth();
int month = xmas.getMonthValue();
int dayOfMonth = xmas.getDayOfMonth();
DayOfWeek dayOfWeek = xmas.getDayOfWeek();
int dayOfYear = xmas.getDayOfYear();

LocalDateTime ldt = LocalDateTime.now();
LocalDateTime xmasLunchTime = LocalDateTime.of(2018, 12, 25, 12, 30, 00);
LocalDateTime xmasLunchTime2 = LocalDateTime.of(xmas, lunchStartTime);
```

일시의 특정 데이터까지만 확인하고 싶을때, 잘라서 사용할 수 있는 API를 제공. `ChronoUnit.MONTH` 부터는 안됨.

```java
LocalDateTime ldt = LocalDateTime.now();
ldt.truncatedTo(ChronoUnit.NANOS);
ldt.truncatedTo(ChronoUnit.MILLIS);
ldt.truncatedTo(ChronoUnit.SECONDS);
ldt.truncatedTo(ChronoUnit.MINUTES);
ldt.truncatedTo(ChronoUnit.HOURS);
ldt.truncatedTo(ChronoUnit.DAYS);
```

타임존에 따른 일시 데이터는 다음과 같이 `java.time.ZonedDateTime` 객체로 처리 가능

```java
ZonedDateTime dateTimeOfSeoul = ZonedDateTime.now(); // 실행환경의 타임존을 따름
ZonedDateTime dateTimeOfParis = ZonedDateTime.now(ZoneId.of("Europe/Paris"));
ZonedDateTime xmasLunchTimeOfBangkok = ZonedDateTime.of(xmasLunchTime, ZoneId.of("Asia/Bangkok"));
ZonedDateTime dateTimeOfBangkok = ZonedDateTime.of(dateTimeOfSeoul.tolDateTime(), ZoneId.of("Asia/Bangkok"));

ZoneDateTime dateTimeOfNewYork = dateTimeOfSeoul.withZoneSameLocal(ZoneId.of("America/Chicago"));  
```

이는 물론 시차에 따른 데이터로도 처리 가능. 타임존이 섬머타임 같은 것도 처리해주므로 위의 예제와 같이 `ZoneId`의 타임존을 이용하는 것이 편리하다함.

```java
ZoneOffset seoulZoneOffset = ZoneOffset.ofHours(9);
ZoneOffset tokyoZoneOffset = ZoneOffset.of("+09:00");
ZonedDateTime.now(seoulZoneOffset);
ZonedDateTime.now(tokyoZoneOffset);
```

여기까지의 내용은 다음과 같이도 표현 가능.

```java
Year year = Year.of(2018);
YearMonth yearMonth = Year.of(2018).atMonth(12);
LocalDate ld = Year.of(2018).atMonth(12).atDay(25);
LoalDateTime ldt = Year.of(2018).atMonth(12).atDay(25).atTime(00, 00);
ZonedDateTime zdt = Year.of(2018).atMonth(12).atDay(25).atTime(00, 00).atZone(ZoneId.of("Europe/London"));
```

문자열로는 다음과 같이 변환 가능.

```java
import java.time.format.*;

ZonedDateTime zdt = Year.of(2018).atMonth(12).atDay(25).atTime(13, 30, 24, 123).atZone(ZoneId.of("Europe/Paris"));
zdt.format(DateTimeFormatter.ISO_DATE_TIME);
zdt.format(DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss z"));
zdt.format(DateTimeFormatter.ofLocalizedDateTime(FormatStyle.FULL));
zdt.format(DateTimeFormatter.ofLocalizedDateTime(FormatStyle.FULL).withLocale(Locale.US));
```